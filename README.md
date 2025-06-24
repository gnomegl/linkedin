# linkedin - LinkedIn API Client

[![Basher](https://img.shields.io/badge/basher-install-brightgreen)](https://github.com/basherpm/basher)

Command-line tool for interacting with LinkedIn profiles, posts, and company data via the LinkedIn API.

## Features

- **Profile Management**: Get profile data, connections, and comprehensive information
- **Post Search**: Search and analyze LinkedIn posts with advanced filtering
- **People Search**: Find people based on keywords and location
- **Company Intelligence**: Search companies and get detailed insights
- **Domain Lookup**: Find companies by domain name
- **Flexible Output**: JSON or formatted output options

## Installation

### Using Basher

```bash
basher install gnomegl/linkedin
```

### Manual Installation

```bash
git clone https://github.com/gnomegl/linkedin.git
cd linkedin
chmod +x bin/linkedin
# Add to PATH or copy to /usr/local/bin
```

## Prerequisites

You need a RapidAPI key for the LinkedIn API:

1. Sign up at [RapidAPI](https://rapidapi.com/)
2. Subscribe to the LinkedIn API
3. Get your API key

## Configuration

Set your API key using one of these methods:

```bash
# Environment variable
export RAPIDAPI_KEY="your-api-key-here"

# Command line option
linkedin --key "your-api-key-here" profile get satyanadella
```

## Usage

### Profile Commands

```bash
# Get basic profile data
linkedin profile get satyanadella

# Get connection count
linkedin profile connections adamselipsky

# Get comprehensive profile data
linkedin profile all adamselipsky
```

### Post Commands

```bash
# Search posts by keyword
linkedin posts search "artificial intelligence"

# Advanced post search with filters
linkedin posts search "machine learning" \
  --sort-by date_posted \
  --date-posted "Past week" \
  --content-type ARTICLE

# Get post with comments
linkedin posts get-with-comments 7181285160586211328
```

### People Search

```bash
# Search for people
linkedin people search "software engineer"

# Search with location filter
linkedin people search "data scientist" --geo 103644278,101165590
```

### Company Commands

```bash
# Get company details
linkedin company get google

# Search companies
linkedin company search --keyword "artificial intelligence" \
  --locations 103644278 \
  --sizes D,E,F,G

# Find company by domain
linkedin company domain apple.com

# Get company insights (premium)
linkedin company insights amazon
```

## Advanced Filtering

### Post Search Filters

- `--sort-by` - Sort by date_posted or relevance
- `--date-posted` - Filter by time period
- `--content-type` - Filter by ARTICLE, IMAGE, VIDEO
- `--from-member` - Filter by author member IDs
- `--from-company` - Filter by company IDs
- `--author-industry` - Filter by industry IDs
- `--author-title` - Filter by job title

### Company Search Filters

- `--locations` - Filter by location Geo IDs
- `--sizes` - Filter by company size codes (D,E,F,G)
- `--industries` - Filter by industry IDs
- `--has-jobs` - Only companies with job postings

## Output Formats

```bash
# JSON output
linkedin --json profile get satyanadella

# Formatted output (default)
linkedin profile get satyanadella
```

## Examples

```bash
# Find AI researchers
linkedin people search "artificial intelligence researcher" \
  --geo 103644278

# Search for recent posts about blockchain
linkedin posts search "blockchain" \
  --sort-by date_posted \
  --date-posted "Past 24 hours"

# Find tech companies in Silicon Valley
linkedin company search \
  --keyword "technology" \
  --locations 103644278 \
  --sizes E,F,G

# Get comprehensive profile analysis
linkedin profile all elonmusk
```

## Requirements

- `curl` - For API requests
- `jq` - For JSON processing

## API Limitations

- Rate limits apply based on your RapidAPI subscription
- Some features require premium API access
- LinkedIn's API policies and availability may change

## License

MIT License
