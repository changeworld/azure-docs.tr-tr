---
title: Log Analytics akıllı analitik örnekleri | Microsoft Dokümanlar
description: Kullanıcı etkinliğinin analizini gerçekleştirmek için Log Analytics'te akıllı analitik işlevlerini kullanan örnekler.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 01/15/2019
ms.openlocfilehash: 51584ccf5f845be8a06b1e049cae11e636edef11
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: tr-TR
ms.lasthandoff: 03/28/2020
ms.locfileid: "77659841"
---
# <a name="log-analytics-smart-analytics-examples"></a>Log Analytics akıllı analitik örnekleri
Bu makalede, kullanıcı etkinliğinin analizini gerçekleştirmek için Log Analytics'te akıllı analitik işlevlerini kullanan örnekler yer almaktadır. Bu örnekleri, Application Insights tarafından izlenen kendi uygulamalarınızı çözümlemek için veya diğer veriler üzerinde benzer analizler için bu sorgularda bulunan kavramları kullanmak için kullanabilirsiniz. 

Bu örneklerde kullanılan farklı anahtar kelimelerle ilgili ayrıntılar için [Kusto dil referansına](https://docs.microsoft.com/azure/kusto/query/) bakın. Log [Analytics'te](get-started-queries.md) yeniyseniz sorgu oluşturma konusunda bir ders tenere gidin.

## <a name="cohorts-analytics"></a>Kohort analizi

Kohort analizi, kohort olarak bilinen belirli kullanıcı gruplarının etkinliğini izler. Geri dönen kullanıcıların oranını ölçerek bir hizmetin ne kadar çekici olduğunu ölçmeye çalışır. Kullanıcılar, hizmeti ilk kez kullandıkları zamana göre gruplandırılır. Kohortları analiz ederken, ilk izlenen dönemlerde aktivitede bir azalma bulmayı bekliyoruz. Her kohort, üyelerinin ilk kez gözlemlendiği haftaya göre anılır.

Aşağıdaki örnek, kullanıcıların hizmeti ilk kullanımlarından sonra 5 hafta boyunca gerçekleştirettikleri etkinlik sayısını analiz eder.

```Kusto
let startDate = startofweek(bin(datetime(2017-01-20T00:00:00Z), 1d));
let week = range Cohort from startDate to datetime(2017-03-01T00:00:00Z) step 7d;
// For each user we find the first and last timestamp of activity
let FirstAndLastUserActivity = (end:datetime) 
{
    customEvents
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    // Check 30 days back to see first time activity
    | where timestamp > startDate - 30d
    | where timestamp < end      
    | summarize min=min(timestamp), max=max(timestamp) by user_AuthenticatedId
};
let DistinctUsers = (cohortPeriod:datetime, evaluatePeriod:datetime) {
    toscalar (
    FirstAndLastUserActivity(evaluatePeriod)
    // Find members of the cohort: only users that were observed in this period for the first time
    | where min >= cohortPeriod and min < cohortPeriod + 7d  
    // Pick only the members that were active during the evaluated period or after
    | where max > evaluatePeriod - 7d
    | summarize dcount(user_AuthenticatedId)) 
};
week 
| where Cohort == startDate
// Finally, calculate the desired metric for each cohort. In this sample we calculate distinct users but you can change
// this to any other metric that would measure the engagement of the cohort members.
| extend 
    r0 = DistinctUsers(startDate, startDate+7d),
    r1 = DistinctUsers(startDate, startDate+14d),
    r2 = DistinctUsers(startDate, startDate+21d),
    r3 = DistinctUsers(startDate, startDate+28d),
    r4 = DistinctUsers(startDate, startDate+35d)
| union (week | where Cohort == startDate + 7d 
| extend 
    r0 = DistinctUsers(startDate+7d, startDate+14d),
    r1 = DistinctUsers(startDate+7d, startDate+21d),
    r2 = DistinctUsers(startDate+7d, startDate+28d),
    r3 = DistinctUsers(startDate+7d, startDate+35d) )
| union (week | where Cohort == startDate + 14d 
| extend 
    r0 = DistinctUsers(startDate+14d, startDate+21d),
    r1 = DistinctUsers(startDate+14d, startDate+28d),
    r2 = DistinctUsers(startDate+14d, startDate+35d) )
| union (week | where Cohort == startDate + 21d 
| extend 
    r0 = DistinctUsers(startDate+21d, startDate+28d),
    r1 = DistinctUsers(startDate+21d, startDate+35d) ) 
| union (week | where Cohort == startDate + 28d 
| extend 
    r0 = DistinctUsers (startDate+28d, startDate+35d) )
// Calculate the retention percentage for each cohort by weeks
| project Cohort, r0, r1, r2, r3, r4,
          p0 = r0/r0*100,
          p1 = todouble(r1)/todouble (r0)*100,
          p2 = todouble(r2)/todouble(r0)*100,
          p3 = todouble(r3)/todouble(r0)*100,
          p4 = todouble(r4)/todouble(r0)*100 
| sort by Cohort asc
```
Bu örnek, aşağıdaki çıktıyla sonuçlanır.

![Kohort analizi çıktısı](media/smart-analytics/cohorts.png)

## <a name="rolling-monthly-active-users-and-user-stickiness"></a>Aylık etkin kullanıcıları ve kullanıcı yapışkanlığını yuvarlama
Aşağıdaki örnekler, kayar pencere hesaplamaları gerçekleştirmenize olanak tanıyan [series_fir](/azure/kusto/query/series-firfunction) işlevi ile zaman serisi çözümlemesi kullanır. İzlenen örnek uygulama, kullanıcıların etkinliklerini özel etkinlikler yoluyla izleyen çevrimiçi bir mağazadır. Sorgu, _addToCart_ ve _Checkout_olmak üzere iki tür kullanıcı aktivitesini izler ve _etkin kullanıcıları_ belirli bir günde en az bir kez kullanıma alan kullanıcılar olarak tanımlar.



```Kusto
let endtime = endofday(datetime(2017-03-01T00:00:00Z));
let window = 60d;
let starttime = endtime-window;
let interval = 1d;
let user_bins_to_analyze = 28;
// Create an array of filters coefficients for series_fir(). A list of '1' in our case will produce a simple sum.
let moving_sum_filter = toscalar(range x from 1 to user_bins_to_analyze step 1 | extend v=1 | summarize makelist(v)); 
// Level of engagement. Users will be counted as engaged if they performed at least this number of activities.
let min_activity = 1;
customEvents
| where timestamp > starttime  
| where customDimensions["sourceapp"] == "ai-loganalyticsui-prod"
// We want to analyze users who actually checked-out in our web site
| where (name == "Checkout") and user_AuthenticatedId <> ""
// Create a series of activities per user
| make-series UserClicks=count() default=0 on timestamp 
    in range(starttime, endtime-1s, interval) by user_AuthenticatedId
// Create a new column containing a sliding sum. 
// Passing 'false' as the last parameter to series_fir() prevents normalization of the calculation by the size of the window.
// For each time bin in the *RollingUserClicks* column, the value is the aggregation of the user activities in the 
// 28 days that preceded the bin. For example, if a user was active once on 2016-12-31 and then inactive throughout 
// January, then the value will be 1 between 2016-12-31 -> 2017-01-28 and then 0s. 
| extend RollingUserClicks=series_fir(UserClicks, moving_sum_filter, false)
// Use the zip() operator to pack the timestamp with the user activities per time bin
| project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
// Transpose the table and create a separate row for each combination of user and time bin (1 day)
| mvexpand RollingUserClicksByDay
| extend Timestamp=todatetime(RollingUserClicksByDay[0])
// Mark the users that qualify according to min_activity
| extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
// And finally, count the number of users per time bin.
| summarize sum(RollingActiveUsersByDay) by Timestamp
// First 28 days contain partial data, so we filter them out.
| where Timestamp > starttime + 28d
// render as timechart
| render timechart
```

Bu örnek, aşağıdaki çıktıyla sonuçlanır.

![Aylık kullanıcı çıktısını kaydetme](media/smart-analytics/rolling-mau.png)

Aşağıdaki örneği, yukarıdaki sorguyu yeniden kullanılabilir bir işleve dönüştürür ve yuvarlanan kullanıcı yapışkanlığını hesaplamak için kullanır. Bu sorgudaki etkin kullanıcılar, yalnızca belirli bir günde en az bir kez check-out yapan kullanıcılar olarak tanımlanır.

``` Kusto
let rollingDcount = (sliding_window_size: int, event_name:string)
{
    let endtime = endofday(datetime(2017-03-01T00:00:00Z));
    let window = 90d;
    let starttime = endtime-window;
    let interval = 1d;
    let moving_sum_filter = toscalar(range x from 1 to sliding_window_size step 1 | extend v=1| summarize makelist(v));    
    let min_activity = 1;
    customEvents
    | where timestamp > starttime
    | where customDimensions["sourceapp"]=="ai-loganalyticsui-prod"
    | where (name == event_name)
    | where user_AuthenticatedId <> ""
    | make-series UserClicks=count() default=0 on timestamp 
        in range(starttime, endtime-1s, interval) by user_AuthenticatedId
    | extend RollingUserClicks=fir(UserClicks, moving_sum_filter, false)
    | project User_AuthenticatedId=user_AuthenticatedId , RollingUserClicksByDay=zip(timestamp, RollingUserClicks)
    | mvexpand RollingUserClicksByDay
    | extend Timestamp=todatetime(RollingUserClicksByDay[0])
    | extend RollingActiveUsersByDay=iff(toint(RollingUserClicksByDay[1]) >= min_activity, 1, 0)
    | summarize sum(RollingActiveUsersByDay) by Timestamp
    | where Timestamp > starttime + 28d
};
// Use the moving_sum_filter with bin size of 28 to count MAU.
rollingDcount(28, "Checkout")
| join
(
    // Use the moving_sum_filter with bin size of 1 to count DAU.
    rollingDcount(1, "Checkout")
)
on Timestamp
| project sum_RollingActiveUsersByDay1 *1.0 / sum_RollingActiveUsersByDay, Timestamp
| render timechart
```

Bu örnek, aşağıdaki çıktıyla sonuçlanır.

![Kullanıcı yapışkanlığı çıktısı](media/smart-analytics/user-stickiness.png)

## <a name="regression-analysis"></a>Regresyon analizi
Bu örnek, yalnızca bir uygulamanın izleme günlüklerine dayalı olarak hizmet kesintileri için otomatik bir dedektörün nasıl oluşturulacak olduğunu gösterir. Dedektör, uygulamada göreceli hata ve uyarı izlerinde anormal ani artışlar arar.

İzleme günlükleri verilerine göre hizmet durumunu değerlendirmek için iki teknik kullanılır:

- Yarı yapılandırılmış metin izleme günlüklerini pozitif ve negatif izleme çizgileri arasındaki oranı temsil eden bir metretmeye dönüştürmek için [make-seri](/azure/kusto/query/make-seriesoperator) kullanın.
- 2 satırlı doğrusal regresyon ile zaman serisi çözümlemesi kullanarak gelişmiş adım atlama algılama gerçekleştirmek için [series_fit_2lines](/azure/kusto/query/series-fit-2linesfunction) ve [series_fit_line](/azure/kusto/query/series-fit-linefunction) kullanın.

``` Kusto
let startDate = startofday(datetime("2017-02-01"));
let endDate = startofday(datetime("2017-02-07"));
let minRsquare = 0.8;  // Tune the sensitivity of the detection sensor. Values close to 1 indicate very low sensitivity.
// Count all Good (Verbose + Info) and Bad (Error + Fatal + Warning) traces, per day
traces
| where timestamp > startDate and timestamp < endDate
| summarize 
    Verbose = countif(severityLevel == 0),
    Info = countif(severityLevel == 1), 
    Warning = countif(severityLevel == 2),
    Error = countif(severityLevel == 3),
    Fatal = countif(severityLevel == 4) by bin(timestamp, 1d)
| extend Bad = (Error + Fatal + Warning), Good = (Verbose + Info)
// Determine the ratio of bad traces, from the total
| extend Ratio = (todouble(Bad) / todouble(Good + Bad))*10000
| project timestamp , Ratio
// Create a time series
| make-series RatioSeries=any(Ratio) default=0 on timestamp in range(startDate , endDate -1d, 1d) by 'TraceSeverity' 
// Apply a 2-line regression to the time series
| extend (RSquare2, SplitIdx, Variance2,RVariance2,LineFit2)=series_fit_2lines(RatioSeries)
// Find out if our 2-line is trending up or down
| extend (Slope,Interception,RSquare,Variance,RVariance,LineFit)=series_fit_line(LineFit2)
// Check whether the line fit reaches the threshold, and if the spike represents an increase (rather than a decrease)
| project PatternMatch = iff(RSquare2 > minRsquare and Slope>0, "Spike detected", "No Match")
```

## <a name="next-steps"></a>Sonraki adımlar

- Dille ilgili ayrıntılar için [Veri Gezgini dil başvurusuna](/azure/kusto/query) bakın.
- [Log Analytics'te sorgu yazma konusunda](get-started-queries.md)bir derste yürüyün.
