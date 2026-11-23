# ACC Reality Check
#### Group Members: Emma Kee (hvh6ng), Kylie Sharp (dhq4mr), and Raffaella Alpaca (bet3at)

### Project Question
Which ACC men's basketball teams are performing as well as their records suggest, and which teams have built their records by playing weaker opponents?

### Project Description
The beginning of every college basketball season can be difficult to evaluate. During November, most teams play their non-conference schedules, which include opponents of varying levels of competition. Some teams face strong opponents, while others schedule smaller programs that they are more likely to defeat.

By the middle of November, several ACC teams may have undefeated records, making them appear similar in the standings. However, a team with a 5–0 record against strong opponents is in a very different position from a team with a 5–0 record against weaker opponents.

This project aims to look beyond win-loss records by combining team ratings, game results, and player statistics to evaluate the strength of each team's performance.

### Project Objectives
Build a small data system that can distinguish between teams with strong records against competitive opponents and teams whose records may be influenced by weaker schedules.

The system will combine a public basketball ratings file with game results and box scores from ESPN's public API, store the collected and processed data in a SQLite database, and publish the results through a web API that others can access.

### What Our Pipeline Does
- **Extract:** Downloads the Bart Torvik CSV and retrieves every ESPN scoreboard date from November 1, 2026, through the most recent completed games. It then downloads the game summary for every finished game. Raw responses are saved locally and reused on subsequent runs.
- **Transform:** Processes the extracted data and records important decisions in a decision log.

  - **Team name matching:** Builds a name crosswalk connecting ESPN team names to their corresponding Bart Torvik names. An automatic first pass, such as lowercasing names and treating "State" and "St." as equivalent, should match most schools. Any remaining unmatched names are identified and corrected manually. Assertions verify that all 18 ACC schools are matched and that no two ESPN teams map to the same Torvik team.
  - **Game filtering:** Keeps only completed games and determines how to handle opponents that do not have a Bart Torvik rating.
  - **Date and time conversion:** Converts game start times from UTC to Eastern Time and uses the Eastern calendar date as the official game date.
  - **Game restructuring:** Reshapes the game data into one row per team per game. Each game appears twice, once from each team's perspective, with points for, points against, scoring margin, and whether the team played at home, away, or at a neutral site.
  - **Statistical calculations:** Splits made-and-attempted statistics into separate numeric columns and calculates estimated possessions for each team in each game.
  - **Player statistics:** Flattens individual player box scores into one row per player per game.

- **Load:** Creates SQLite tables with primary keys and loads the processed data using INSERT OR REPLACE. This ensures that rerunning the pipeline updates existing records instead of creating duplicates. The final notebook will demonstrate that running the load process twice leaves all database row counts unchanged.

### The Data We Are Working With

| File | Format | Description | Approximate Size |
|------|--------|-------------|-----------------|
| [Bart Torvik's Team Ratings](https://barttorvik.com/2027_team_results.csv) | CSV | Daily Division I basketball team ratings, including offensive and defensive efficiency, tempo, and strength of schedule. | ~365 rows |
| [ESPN Scoreboard API](https://site.api.espn.com/apis/site/v2/sports/basketball/mens-college-basketball/scoreboard?groups=2&dates=20261114&limit=200) | JSON API | Retrieves games played by ACC teams on a specific date, including game IDs, opponents, dates, and scores. | Varies by date |
| [ESPN Game Summary API](https://site.api.espn.com/apis/site/v2/sports/basketball/mens-college-basketball/summary?event=401920307) | JSON API | Retrieves the detailed box score for an individual game, including team and player statistics. | One game per request |

### Our Database: ```acc.db```
The database stores ratings for every Division I team, not just ACC teams, because evaluating the strength of ACC schedules requires ratings for their opponents as well.
| Table | One Row Per | Purpose |
|------|--------|-------------|
| ```teams``` | Team | Stores team information, including ESPN ID, ESPN name, Bart Torvik name, and ACC membership. |
| ```team_crosswalk``` | Source team name | Maps different ESPN and Bart Torvik team name spellings to the same team. |
| ```ratings``` | Team per snapshot date | Stores Bart Torvik ratings for each team on each date the data is downloaded. |
| ```games``` | Game | Stores the game date, UTC start time, home and away teams, final scores, and whether the game was played at a neutral site. |
| ```team_games``` | Team per game | Stores points for and against, scoring margin, box score totals, and estimated possessions from each team's perspective. |
| ```player_games``` | Player per game | Stores individual player statistics, including minutes, points, rebounds, assists, steals, blocks, and turnovers. |

### Dates We Pulled the Source URLs

### How to Run the Project

### Live API Address With Its Routes

---

### Presentation Video Link: 

---

### Reflection
**The Crosswalk** \
_Which names failed to match, how did you fix them, and what would have happened to your answers if one ACC school had gone unmatched?_

**A Pipeline Decision** \
_Choose one decision, such as how you handled unrated opponents, how you estimated possessions, or how you assigned game dates. What else could you have done, and how might your results have changed?_

**Trust** \
_If your pipeline ran automatically every morning for the rest of the season, what would break first, and how would you find out?_

**Our Numbers and Torvik's** \
_Describe a place where your own calculation and Torvik's rating told different stories. Which did you believe, and why?_

**Teamwork** \
_How did your team divide the work, and what would you change with another week?_
