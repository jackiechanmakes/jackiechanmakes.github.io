### Project: Measuring the Impact of the Bangladesh Internet Outage

**Description of Event:**

In mid-July 2024, protests led by university students erupted in Bangladesh following a High Court decision. The High Court’s ruling would reinstate a quota system, resulting in 56% of government jobs to be set aside for specific demographics and a mere 44% of positions to be filled by merit [1]. The reservation of civil service positions for groups of people such as women, people with disabilities, and ethnic minorities was generally understood and tolerated by the Bengali people. The protesters, however, were angered by the significant 30% allocation of positions to the descendants of fighters who participated in the 1971 Bangladesh Liberation War, which was the war against East Pakistan that led to Bangladesh's independence [2]. With many of the original fighters already retired or dead, protesters felt it was unfair to extend this benefit to grandchildren, who had not been directly involved in the war. These feelings were inflamed by the serious wealth disparity and corruption issues that have historically been at play in Bangladesh with many conjecturing the quota system reinstatement favored those of the ruling party [3]. Tensions also increased because youth unemployment rates were at an all-time high and civil service positions were known to be the most coveted with its prestigiousness and stability. 

Protestors’ initial outcry for an appeal was peaceful; however, they were swiftly met with police brutality, deployment of the armed forces, and the invocation of a "shoot-at-sight" curfew against protestors who were only armed with rocks and sticks. With the rapid deterioration of law and order, Prime Minister Sheikh Hasina, whose father originally created the quota system, ordered a nationwide network outage on July 18, 2024 to quell unrest and stop further uprising [4]. The internet blackout isolated protestors from each other and prevented students from posting incriminating evidence against the police. It was also used to stop the media from disseminating information within and outside of Bangladesh such as the estimated death toll of 300 to 500 people made up mostly of students and children [2]. Although protests lasted until August 5, 2024, broadband service started recovering on July 24, 2024 with mobile connectivity following suit on July 28, 2024 [5]. Because the internet outage impacted people’s ability to withdraw money and work, this move was primarily done for the sake of banks and business establishments with social media websites still being blocked during this time period. On August 4, 2024, there was another outage, although short-lived, after protests rose in violence; however, all network connections were promptly recovered the next day after Prime Minister Hasina resigned and fled the country. With the Prime Minister’s loss of power, the movement concluded with an agreement that 93% of positions were to be merit-based and 7% of positions were to be reserved for specific demographics, resulting in a reduction from 30% to 5% for descendants of war veterans [6]. 

<u>References:</u>

1. https://www.nytimes.com/2024/07/21/world/asia/bangladesh-protests-quotas-jobs.html
2. https://en.wikipedia.org/wiki/2024_Bangladesh_quota_reform_movement
3. https://www.aljazeera.com/news/2024/7/21/bangladesh-court-scraps-most-job-quotas-that-caused-deadly-unrest-reports
4. https://en.wikipedia.org/wiki/2024_Bangladesh_internet_blackouts
5. https://www.aljazeera.com/news/2024/7/28/bangladesh-restores-mobile-internet-after-11-day-blackout-to-quell-protests
6. https://en.wikipedia.org/wiki/Quota_system_of_Bangladesh_Civil_Service

**Event time period:**

The internet outage started on July 18, 2024. Broadband services recovered on July 24, 2024. Mobile connections came back online on July 28, 2024. There was a brief outage on August 4, 2024 that was restored after the Prime Minister resigned and fled the country on August 5, 2024.

```python
import pybgpstream
from matplotlib import pyplot as plt
from datetime import datetime, timezone

stream = pybgpstream.BGPStream(
    from_time="2024-07-16 08:00:00 UTC", until_time="2024-07-26 08:00:00 UTC",
    collectors=["route-views.chicago"],
    record_type="ribs"
)

prefix_set = set()
snapshot_per_hour_dict = {}
current_day = "2024-07-26 08"
for elem in stream:
    time = datetime.fromtimestamp(elem.time, timezone.utc)
    date = time.strftime("%Y-%m-%d %H")

    if date != current_day:
        snapshot_per_hour_dict[current_day] = len(prefix_set)
        print(snapshot_per_hour_dict)
        prefix_set = set()
        current_day = date

    if "as-path" in elem.fields:
        as_path = elem.fields["as-path"].split()
        if "24389" == as_path[-1]:
            prefix_set.add(elem.fields["prefix"])

print(snapshot_per_hour_dict)
```

Output: 
```bash
{'2024-07-26 08': 95}
```

<u>References:</u>
- https://en.wikipedia.org/wiki/2024_Bangladesh_internet_blackouts
- https://developingtelecoms.com/telecom-business/telecom-regulation/17105-bangladesh-switches-off-mobile-internet-again-as-protests-escalate-2.html

**Networks that were involved/impacted:**
AS24389 (Grameenphone), AS25245 (Banglalink), AS24432 (Robi Axiata), AS58715 (Earth Telecommunication), AS63526 (Carnival Internet) 

```python
from matplotlib.ticker import AutoMinorLocator, MultipleLocator
import matplotlib.dates as mdates
import numpy as np

# Convert time to datetime object 
timestamp = [datetime.strptime(time, '%Y-%m-%d %H') for time in snapshot_per_hour_dict.keys()]
prefix_counts = snapshot_per_hour_dict.values()

plt.style.use("default")

fig, ax = plt.subplots()
ax.plot(timestamp, prefix_counts, color="blue", marker="o", linestyle="-", label="Number of Prefixes")
ax.set_title('AS24389 (Grameenphone)')

ax.set_xlabel("Time", fontsize=10, fontweight='bold')
ax.set_ylabel("Number of Advertised Prefixes", fontsize=10, fontweight='bold')

ax.xaxis.set_minor_locator(AutoMinorLocator())
ax.yaxis.set_minor_locator(AutoMinorLocator())

ax.xaxis.set_major_formatter(mdates.DateFormatter('%b %d\n%H:%M'))
ax.set_yticks((np.arange(50, 140, 10))) # Adjust scale

ax.grid(visible=True)
ax.tick_params("x", which='major', length=7, labelsize=8, rotation=45)
ax.tick_params("y", which='major', length=7, labelsize=8)
ax.tick_params(which='minor')

plt.show()
```

<u>References:</u>
- https://blog.cloudflare.com/a-recent-spate-of-internet-disruptions-july-2024/

**Conclusion:**
As evidenced, there was no internet network activity in Bangladesh during the protests. 