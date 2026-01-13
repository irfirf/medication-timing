# Medication Timing Tool
Creating a tool that can help me determine the optimal times to take my medicaiton

## How I calculate the values
Table 1 and figure 1 for Methylphenidate Hydrochloride ER, from [FDA](https://fda.report/DailyMed/768bf181-d0d8-49e5-9c87-cacbb069e0b2/#section-12.3).

TABLE 1: Mean ± SD Pharmacokinetic Parameters
| Parameters | Methylphenidate Hydrochloride Extended-Release Tablets (18 mg once daily) (n=36) | Methylphenidate (5 mg three times daily) (n=35) |
|------------|-----------------------------|------------------------------------|
| Cmax (ng/mL) | 3.7 ± 1.0 | 4.2 ± 1.0 |
| Tmax (h) | 6.8 ± 1.8 | 6.5 ± 1.8 |
| AUCinf (ng•h/mL) | 41.8 ± 13.9 | 38.0 ± 11.0 |
| t½ (h) | 3.5 ± 0.4 | 3.0 ± 0.5 |

![Mean Plasma Concentration Over Time for Methylphenidate ER versus Methylphenidate IR](https://fda.report/DailyMed/768bf181-d0d8-49e5-9c87-cacbb069e0b2/MPD+ER+72+mg-figure+1.jpg) <br>
FIGURE 1: Mean methylphenidate plasma concentrations in 36 adults, following a single dose of methylphenidate hydrochloride extended-release tablets 18 mg once daily and immediate-release methylphenidate 5 mg three times daily administered every 4 hours.

I looked everywhere to obtain the dataset the FDA used to create figure 1, but unfortunatly it seems like that information is proprietary.
However, I did find a figure that had readable axises from [Park-Wyllie et al.](https://www.sciencedirect.com/science/article/pii/S0149291817309396#f0010) (figure 2). Ironically, Park-Wyllie et al. argue that Health Canada should reconsider its approval of bioequivalence between Concerta (OROS-MPH) and generic (MPH ER-C). This study was published in 2017 and, as of January 2026 (as far as I'm aware), Health Canada has not reconsidered. So, for my applications, I will consider Concerta and generic as the same.

![Mean Plasma Concentration Over Time and Concentraion Ratio for Methylphenidate OROS versus ER-C versus SR](https://ars.els-cdn.com/content/image/1-s2.0-S0149291817309396-gr2_lrg.jpg)
FIGURE 2: A, Mean dose-corrected plasma methylphenidate (MPH) concentrations over time with the use of MPH extended release (MPH ER-C; generic), Osmotic Release Oral System (ORS-MPH; branded), and MPH sustained release (MPH-SR) (n = 24). B, Mean dose-corrected ratios of plasma methylphenidate concentration over time with MPH ER-C/OROS-MPH, MPH ER-C/MPH-SR, and MPH-SR/OROS-MPH (n = 24).

Using figure 2, I created a table of values (table 2) to help me recreate the MPH ER-C.
<br>
TABLE 2: Estimated 54mg MPH ER-C Values

| Time (h) | Mean Plasma Concentration (ng/mL) |
|----------|-----------------------------------|
| 0.0 | 0.0 |
| 0.5 | 1.5 |
| 1.0 | 7.0 |
| 1.5 | 10.5 |
| 2.0 | 10.5 |
| 2.5 | 11.5 |
| 3.0 | 13.5 |
| 3.5 | 13.0 |
| 4.0 | 13.0 |
| 5.0 | 14.0 |
| 6.0 | 12.0 |
| 7.0 | 11.0 |
| 8.0 | 9.5 |
| 10.0 | 7.5 |
| 11.0 | 6.5 |
| 12.0 | 5.5 |
| 14.0 | 4.0 |
| 16.0 | 3.0 |
| 20.0 | 2.0 |
| 24.0 | 1.0 |

The FDA states that "Following oral administration [, methylphenidate ER-C reaches] an initial maximum at about 1 hour, followed by gradual ascending concentrations over the next 5 to 9 hours, after which a gradual decrease begins". This, and my own anecdotal experience, is why I will be breaking down the curves into three parts: initial $(0 < h <= 1)$, peak $(1 < h <= 5)$, and decline $(h > 5)$. <br>
Plotting table 2 will give us figure 3.

![Estimated Mean Plasma Concentration Graph](./Graphs/estimatedMeanPlasmaConcentrationGraph.png)<br>
FIGURE 3: Estimated 54mg MPH ER-C values plotted with a moving average trendline, $c(t)$.

Due to my lack of domain knowledge in pharmokinetics and to apply our math into a tangable tool quickly, I will greatly simply the shape and equation of the curve produced in figure 3. The simplest way to model this curve is by assuming everything to the left of the peak is a polynomial curve and everything to the right of the peak is an expontial decay curve. Using Javascript to model this piecewise funciton, we get the following:


