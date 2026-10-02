# TV Show Records Summary

This program downloads TV show records from TVmaze and calculates show counts by genre and average ratings by language. These summaries help compare the genres and ratings represented in the downloaded records.

## Data source

https://api.tvmaze.com/shows?page=0

Each record represents one TV show. The selected page returns approximately 250 records, although the exact number may change. The program requires at least 50 usable records.

## Setup

Create a virtual environment:

```bash
python -m venv .venv
```

Activate it in Git Bash on Windows:

```bash
source .venv/Scripts/activate
```

On macOS or Linux:

```bash
source .venv/bin/activate
```

Install the dependencies:

```bash
python -m pip install -r requirements.txt
```

## Run

```bash
python records.py
```

The program writes summary.json beside records.py and displays the number of records processed.

If downloading or writing fails, it displays a clear error message and exits with a nonzero status.

## Example output

Add a short excerpt from your generated summary.json here before submission.

The output includes the source URL, the number of records processed, show counts by genre, and average ratings by language.

A show can belong to several genres, so the total genre count can exceed the number of shows. The rated_shows value gives the number of valid ratings included in each language average.

## Data quirks

- A show can have multiple genres. The program counts it once in each valid genre.
- Empty, missing, or malformed genre lists are reported separately rather than assigned an invented genre.
- Sets remove duplicate genre labels within an individual show.
- Invalid entries inside genre lists are counted separately.
- Ratings are nested inside the rating object. The program safely handles missing or malformed rating objects.
- Missing, nonnumeric, Boolean, and out-of-range ratings are excluded rather than treated as zero.
- Missing or blank languages are reported separately and excluded from language averages.
- Rating exclusions count shows that have a valid language but lack a valid rating. Shows without a valid language are counted in the separate language exclusion field.
- A language with no valid ratings receives a null average and a rated_shows count of zero.
- Network information is not required for either aggregation, so a missing network does not prevent processing.
- Records that are not dictionaries are skipped and counted separately.

## Design choices

Lists hold the downloaded records and the filtered records because the program needs to iterate over individual shows.

Dictionaries group genre counts and rating totals by language. They also hold the final summary.

Sets remove duplicate genre labels so one show cannot be counted twice within the same genre.

Comprehensions build the filtered record list, valid genre sets, and final language summary.

Each function has one responsibility and a docstring. Aggregation functions accept records and return results without downloading, printing, or writing files.

The download uses requests with a timeout and raise_for_status. The output uses pathlib, UTF-8 encoding, and JSON indentation of two spaces.

## Known limitations

The program downloads only the selected API page, so the results do not represent the full TVmaze catalogue.

Records and ratings may change between runs.

Language averages give equal weight to each show's rating. They are not averages of individual viewer votes.

The program does not retry failed downloads or use cached data.

If a run fails, an existing summary.json from an earlier successful run remains unchanged. The terminal message indicates whether the current run succeeded.