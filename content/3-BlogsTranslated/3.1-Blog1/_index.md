---
title: "Blog 1"
date: "`r Sys.Date()`"
weight: 1
chapter: false
pre: " <b> 3.1. </b> "
---


# Monitoring Server Health with Amazon GameLift Servers

**Author:** Brian Schuster | **Date:** 20 NOV 2025 | **Category:** Amazon GameLift, Amazon Managed Grafana, Game Development, Games, Management Tools

Running a successful multiplayer game means constantly balancing performance, scalability, and player experience. As the player base grows, new challenges emerge. The causes could stem from various issues: perhaps something in your server code. There could be a memory leak, inefficient logic, or a bug that only surfaces under specific conditions.

Additionally, optimizing latency using Amazon GameLift Servers is important, by strategically allocating capacity across regions to ensure players connect to servers closest to them. However, even with latency optimizations in place, you may still receive feedback about poor performance or degraded gameplay from a subset of players.

We have previously discussed how to diagnose and address broader network issues, but latency metrics alone won’t provide the full picture. You need deeper visibility into what is happening on your game servers.

### Server-side observability: beyond latency

When using Amazon GameLift Servers with telemetry enabled, your game servers collect a variety of detailed metrics — including resource utilization (CPU, memory, network), game-specific metrics (such as tick rate), and custom metrics you define. These metrics are captured at different levels so you can analyze performance at the process, container, host, or fleet-wide level. They can be sent to your chosen metrics storage and visualized with your preferred tools.

We will demonstrate using **Amazon Managed Grafana** with pre-built dashboards. The setup is streamlined and can be completed in under an hour.

The real power lies not only in the metrics themselves but also in how the dashboards help you quickly identify and diagnose issues.

---

### Troubleshooting game server crashes

A common scenario: game sessions are crashing, players are disconnected mid-match, and you need to determine why.

If you haven’t already set up monitoring dashboards, follow the **Configure Amazon Grafana** guide — it takes a few minutes to provision pre-built dashboards for your fleet. Note that we are following the C++ SDK steps, adjust for your chosen implementation.

Once configured, open Amazon Managed Grafana from the AWS Management Console and navigate to the **EC2 Fleet Overview dashboard**. Figure 1 shows a crash event in the Game Server Crashes graph, while the **Crashed Game Sessions** section displays details of the specific crashed game session. Both the instance and the crashed session are linked for deeper investigation.

![EC2 Fleet Overview dashboard showing a crashed Game Session.](../../images/3-BlogsTranslated/1.png)
**Figure 1:** EC2 Fleet Overview dashboard showing a crashed game session.

Select the affected instance. The memory graph (Figure 2) shows memory spiked sharply, then dropped as the process crashed — a signature of a memory leak. Looking at session-level details, you can see one session used significantly more memory.

![Instance Performance dashboard showing memory leak.](../../images/3-BlogsTranslated/2.png)
**Figure 2:** Instance Performance dashboard showing memory leak.

Clicking on the crashed game session opens the **Server Performance dashboard** (Figure 3), showing the session’s resource usage up to the moment of crash. The dashboard indicates this session was responsible for the memory leak.

![Server Performance dashboard showing memory leak.](../../images/3-BlogsTranslated/3.png)
**Figure 3:** Server Performance dashboard showing memory leak.

Each graph includes tooltips explaining what to look for and how to interpret the data. The next step is clear: investigate the logs of the crashed session to identify the trigger, whether from a specific game mode or a player’s action. Metrics guide you to the right logs.

---

### Troubleshooting high CPU usage

Another scenario: players report stuttering gameplay but no crashes. The issue might be CPU-related.

Switch to the **EC2 Instances Overview dashboard** in Amazon Managed Grafana. Figure 4 shows the top 20 EC2 instances by CPU consumption. Most hover around 2–3% CPU, but a few reach 20–30% or higher.

![EC2 Instances Overview dashboard.](../../images/3-BlogsTranslated/4.png)
**Figure 4:** EC2 Instances Overview dashboard

Select a high-CPU instance. The dashboard breaks down CPU by game session (Figure 5), immediately showing which session is consuming the most resources. You can then review logs for that session, focusing on periods of elevated CPU usage.

![Instance Performance dashboard showing top CPU consuming game sessions.](../../images/3-BlogsTranslated/5.png)
**Figure 5:** Instance Performance dashboard showing top CPU-consuming game sessions.

You may find high CPU correlates with intense combat scenarios or a pathfinding bug causing excessive calculations. Metrics alone don’t tell you exactly what’s wrong, but they indicate where to look.

---

### Container support

If running your GameLift Servers Fleet with containers instead of EC2, the same troubleshooting approach applies. Figure 6 is the **Container Fleet Overview dashboard**, showing tasks with high CPU or memory usage.

![Container Fleet Overview dashboard.](../../images/3-BlogsTranslated/6.png)
**Figure 6:** Container Fleet Overview dashboard

Click a specific task, and the **Container Performance dashboard** (Figure 7) breaks down metrics by individual containers. You can see if the game server container is consuming resources as expected or if a sidecar container is causing issues. This granularity helps quickly isolate problems, whether running on EC2 or containers.

![Container Performance dashboard.](../../images/3-BlogsTranslated/7.png)
**Figure 7:** Container Performance dashboard

---

### Next steps

Beyond built-in hardware and game metrics (tick rate, crashes), you can extend monitoring with **custom metrics**.

Examples of game-specific metrics:

- **Combat balance:** Average time-to-kill or DPS by weapon type to detect overpowered weapons after a patch.  
- **Progression blockers:** Success rate of critical quests or bosses to detect bugs blocking progression.  
- **Economy health:** Currency inflation or item acquisition patterns to spot exploits.  
- **AI pathfinding duration:** Time spent calculating NPC paths to detect complex scenarios affecting performance.

After instrumenting custom metrics, create alerts in Amazon Managed Grafana for both system and game-specific thresholds. Examples:

- Memory > 90%  
- CPU > 85% sustained  
- Increase in crashed sessions  
- Spikes in custom metrics (e.g., failed boss attempts)

When an alert triggers, click into the relevant dashboard to investigate — catching issues before players notice.

---

### Conclusion

Optimizing latency connects players to the right location, but **server-side observability** ensures a smooth in-game experience. GameLift Servers telemetry with pre-built dashboards provides visibility to quickly diagnose crashes, performance bottlenecks, and resource issues — without being overwhelmed by raw metrics.

Next time a player complains about a glitchy experience, you’ll know exactly where to look, and with proactive alerting, you’ll catch the issue before they notice.

**Contact an AWS Representative** to learn how we can help accelerate your business.

---

**Further reading:**

- [Getting started with Amazon GameLift Servers](https://aws.amazon.com/gamelift/getting-started/)  
- [Setting up alerts in Amazon Managed Grafana](https://docs.aws.amazon.com/grafana/latest/userguide/AMG.html)  
- [Available Amazon GameLift Servers dashboards and metrics](https://docs.aws.amazon.com/gamelift/latest/developerguide/metrics-dashboards.html)  

**Author:** Brian Schuster, Principal Engineer at AWS for Amazon GameLift.