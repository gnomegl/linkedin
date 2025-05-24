#!/usr/bin/env bash

# @describe Interact with the LinkedIn Data API
# @option -k --key <key> "Your RapidAPI Key for LinkedIn API (or set LINKEDIN_API_KEY)"
# @option -h --host <host> "RapidAPI host for LinkedIn API" @default "linkedin-api8.p.rapidapi.com"
# @flag   -j --json "Output raw JSON (skip jq pretty-printing)"
# @meta require-tools curl,jq

# @cmd Interact with LinkedIn profiles
profile() {
  :
}

# @cmd Get basic profile data
# @arg username! "LinkedIn profile username (e.g., satyanadella)"
profile::get() {
  local endpoint="/?username=${argc_username}"
  make_request "GET" "$endpoint"
}

# @cmd profile connections Get profile connection count
# @arg username! "LinkedIn profile username (e.g., adamselipsky)"
profile::connections() {
  local endpoint="/connection-count?username=${argc_username}"
  make_request "GET" "$endpoint"
}

# @cmd profile all Get profile data, connection count, and posts
# @arg username! "LinkedIn profile username (e.g., adamselipsky)"
profile::all() {
  local endpoint="/profile-data-connection-count-posts?username=${argc_username}"
  make_request "GET" "$endpoint"
}

# @cmd Interact with LinkedIn posts
posts() {
  :
}

# @cmd posts search Search for posts based on criteria
# @arg keyword! "Keyword to search for in posts"
# @option --sort-by <sort> "Sort results (date_posted, relevance)" @default "date_posted"
# @option --date-posted <period> "Filter by date posted (e.g., 'Past 24 hours', 'Past week', 'Past month')"
# @option --page <num> "Page number for results" @default 1
# @option --content-type <type> "Filter by content type (e.g., ARTICLE, IMAGE, VIDEO)"
# @option --from-member <ids> "Filter by author member IDs (comma-separated URNs like ACoAA...)"
# @option --from-company <ids> "Filter by author company IDs (comma-separated numbers like 1441,1035)"
# @option --mentions-member <ids> "Filter by mentioned member IDs (comma-separated URNs)"
# @option --mentions-org <ids> "Filter by mentioned organization IDs (comma-separated numbers)"
# @option --author-industry <ids> "Filter by author industry IDs (comma-separated numbers like 96,4)"
# @option --author-company <ids> "Filter by author company IDs (comma-separated numbers)"
# @option --author-title <title> "Filter by author title (string)"
posts::search() {
  local endpoint="/search-posts"

  # Start building the JSON payload with mandatory fields
  json_payload=$(jq -n \
    --arg keyword "$argc_keyword" \
    --arg sortBy "$argc_sort_by" \
    --argjson page "$argc_page" \
    '{ keyword: $keyword, sortBy: $sortBy, page: $page }')

  # Add optional fields conditionally using a helper function
  json_payload=$(add_json_field "$json_payload" "datePosted" "$argc_date_posted" "string")
  json_payload=$(add_json_field "$json_payload" "contentType" "$argc_content_type" "string")
  json_payload=$(add_json_field "$json_payload" "fromMember" "$argc_from_member" "string_array")
  json_payload=$(add_json_field "$json_payload" "fromCompany" "$argc_from_company" "number_array")
  json_payload=$(add_json_field "$json_payload" "mentionsMember" "$argc_mentions_member" "string_array")
  # API example used "mentionsOrganization", mapping --mentions-org to it
  json_payload=$(add_json_field "$json_payload" "mentionsOrganization" "$argc_mentions_org" "number_array")
  json_payload=$(add_json_field "$json_payload" "authorIndustry" "$argc_author_industry" "number_array")
  json_payload=$(add_json_field "$json_payload" "authorCompany" "$argc_author_company" "number_array")
  json_payload=$(add_json_field "$json_payload" "authorTitle" "$argc_author_title" "string")

  # Ensure the final payload is valid JSON (jq handles this)
  make_request "POST" "$endpoint" "$json_payload"
}

# @cmd posts get-with-comments Get a specific post with its comments
# @arg urn! "Post URN (e.g., 7181285160586211328)"
posts::get_with_comments() {
  local endpoint="/get-profile-post-and-comments?urn=${argc_urn}"
  make_request "GET" "$endpoint"
}

# @cmd Interact with LinkedIn people
people() {
  # When no subcommand is provided, show available subcommands
  if [[ -z "${argc_subcommand:-}" ]]; then
    echo "Available people subcommands:"
    echo "  search    - Search for people based on criteria"
    exit 0
  fi
}

# @cmd people search Search for people based on criteria
# @arg keywords! "Keywords to search for (e.g., 'software engineer')"
# @option --start <num> "Starting position for results" @default 0
# @option --geo <ids> "Filter by location Geo IDs (comma-separated numbers like 103644278,101165590)"
# @option --page <num> "Page number for results" @default 1
people::search() {
  # Fix: Ensure there's no space between endpoint and query params
  local endpoint="/search-people"
  local query_params="?keywords=${argc_keywords}&start=${argc_start}"

  # Add page parameter if provided
  if [[ -n "$argc_page" ]]; then
    query_params="${query_params}&page=${argc_page}"
  fi

  # Add geo parameter if provided
  if [[ -n "$argc_geo" ]]; then
    query_params="${query_params}&geo=${argc_geo}"
  fi

  # Debug: Uncomment to check what's being sent to make_request
  # echo "DEBUG: Endpoint: ${endpoint}${query_params}" >&2

  make_request "GET" "${endpoint}${query_params}"
}

# @cmd Interact with LinkedIn companies
company() {
  :
}

# @cmd company get Get details for a specific company
# @arg username! "Company username/handle (e.g., google)"
company::get() {
  local endpoint="/get-company-details?username=${argc_username}"
  make_request "GET" "$endpoint"
}

# @cmd company search Search for companies based on criteria
# @option --keyword <keyword> "Keyword to search for in company name/description" @default ""
# @option --locations <ids> "Filter by location Geo IDs (comma-separated numbers like 103644278)"
# @option --sizes <codes> "Filter by company size codes (comma-separated, e.g., D,E,F,G)"
# @flag   --has-jobs "Only include companies with job postings"
# @option --industries <ids> "Filter by industry IDs (comma-separated numbers like 96,4)"
# @option --page <num> "Page number for results" @default 1
company::search() {
  local endpoint="/companies/search"
  local json_payload

  # Start building the JSON payload with mandatory fields (page is always sent)
  json_payload=$(jq -n \
    --arg keyword "$argc_keyword" \
    --argjson page "$argc_page" \
    '{ keyword: $keyword, page: $page }') # Start with keyword and page

  # Add optional fields conditionally
  json_payload=$(add_json_field "$json_payload" "locations" "$argc_locations" "number_array")
  json_payload=$(add_json_field "$json_payload" "companySizes" "$argc_sizes" "string_array") # API uses "companySizes", codes are strings
  json_payload=$(add_json_field "$json_payload" "industries" "$argc_industries" "number_array")
  # Handle boolean flag --has-jobs
  if [[ "$argc_has_jobs" == "true" ]]; then
    json_payload=$(add_json_field "$json_payload" "hasJobs" "true" "boolean")
  fi

  make_request "POST" "$endpoint" "$json_payload"
}

# @cmd company domain Find a company by its domain name
# @arg domain! "Company domain (e.g., apple.com)"
company::domain() {
  local endpoint="/get-company-by-domain?domain=${argc_domain}"
  make_request "GET" "$endpoint"
}

# @cmd company insights Get premium company insights (requires appropriate API plan)
# @arg username! "Company username/handle (e.g., amazon)"
company::insights() {
  local endpoint="/get-company-insights?username=${argc_username}"
  make_request "GET" "$endpoint"
}

# --- Helper Functions ---

# Function to exit with an error message
die() {
  echo "Error:" "$@" >&2
  exit 1
}

# Helper function to add a field to a JSON object if the value is not empty
# Usage: add_json_field <current_json> <key> <value> <type>
# type can be: string, number, string_array, number_array, boolean
add_json_field() {
  local current_json="$1" key="$2" value="$3" type="${4:-string}"

  if [[ -n "$value" ]]; then
    # Prepare value based on type
    local jq_filter
    local jq_args=()

    jq_args+=(--arg k "$key")

    case "$type" in
    string)
      jq_args+=(--arg v "$value")
      jq_filter='. + {($k): $v}'
      ;;
    number)
      jq_args+=(--argjson v "$value") # Use argjson for numbers
      jq_filter='. + {($k): $v}'
      ;;
    string_array)
      # Split comma-separated string into JSON array of strings
      local array_json=$(echo "$value" | jq -Rc 'split(",") | map(select(. != ""))') # Ensure empty strings aren't included
      # Only add if array is not empty
      if [[ "$(echo "$array_json" | jq 'length')" -gt 0 ]]; then
        jq_args+=(--argjson v "$array_json")
        jq_filter='. + {($k): $v}'
      else
        # If array would be empty, don't add the key
        echo "$current_json"
        return
      fi
      ;;
    number_array)
      # Split comma-separated string into JSON array of numbers
      # Attempt conversion to number, keep as string if fails (robustness)
      local array_json=$(echo "$value" | jq -Rc 'split(",") | map(select(. != "") | tonumber? // .)')
      # Only add if array is not empty
      if [[ "$(echo "$array_json" | jq 'length')" -gt 0 ]]; then
        jq_args+=(--argjson v "$array_json")
        jq_filter='. + {($k): $v}'
      else
        echo "$current_json"
        return
      fi
      ;;
    boolean)
      # Value should be 'true' or 'false' string, pass via argjson
      jq_args+=(--argjson v "$value")
      jq_filter='. + {($k): $v}'
      ;;
    *)
      # Unknown type, return original JSON
      echo "$current_json"
      return
      ;;
    esac
    # Apply the filter
    echo "$current_json" | jq "${jq_args[@]}" "$jq_filter"
  else
    # Value is empty, return original JSON
    echo "$current_json"
  fi
}

# Function to make API requests using curl
# Usage: make_request <METHOD> <ENDPOINT_PATH> [JSON_DATA]
make_request() {
  local method="$1"
  local endpoint_path="$2"
  local json_data="${3:-}"
  local host="${argc_host:-linkedin-api8.p.rapidapi.com}"
  local base_url="https://${host}"
  local full_url="${base_url}${endpoint_path}"

  # Debug: Uncomment to see the full URL
  # echo "DEBUG: Full URL: ${full_url}" >&2

  local curl_opts=(-s) # Silent but show errors
  local headers=()
  headers+=(-H "x-rapidapi-host: ${host}")
  headers+=(-H "x-rapidapi-key: ${API_KEY:-$LINKEDIN_API_KEY}")

  # Add method-specific options and headers
  if [[ "$method" == "POST" ]]; then
    curl_opts+=(-X POST)
    headers+=(-H "Content-Type: application/json")
    [[ -n "$json_data" ]] && curl_opts+=(--data-raw "$json_data")
  elif [[ "$method" == "GET" ]]; then
    curl_opts+=(-X GET)
  else
    die "Unsupported HTTP method: $method"
  fi

  # Execute curl with more detailed error reporting
  local response
  local status_code

  # Use a temp file to capture response
  local tmp_file=$(mktemp)
  status_code=$(curl -o "$tmp_file" -w "%{http_code}" "${curl_opts[@]}" "${headers[@]}" "$full_url" 2>&1)
  local curl_exit_code=$?
  response=$(<"$tmp_file")
  rm "$tmp_file"

  # Check for curl errors first
  if [[ $curl_exit_code -ne 0 ]]; then
    echo "Curl error (code $curl_exit_code): $status_code" >&2
    echo "Full URL was: $full_url" >&2
    exit 1
  fi

  # Check HTTP status code
  if [[ "$status_code" -lt 200 || "$status_code" -ge 300 ]]; then
    echo "API Error: HTTP status code $status_code" >&2
    echo "Full URL was: $full_url" >&2
    if [[ -n "$response" ]]; then
      if echo "$response" | jq '.' >/dev/null 2>&1; then
        echo "$response" | jq '.' >&2
      else
        echo "$response" >&2
      fi
    fi
    exit 1
  fi

  # Output the response
  if [[ "$argc_json" == "true" ]]; then
    echo "$response"
  else
    if echo "$response" | jq '.' >/dev/null 2>&1; then
      echo "$response" | jq '.'
    else
      echo "$response"
    fi
  fi
}

# --- Main Script Logic ---

# Parse arguments using argc
eval "$(argc --argc-eval "$0" "$@")"

# Check for required tools
if ! command -v curl &>/dev/null; then die "curl is not installed. Please install it."; fi
if ! command -v jq &>/dev/null; then die "jq is not installed. Please install it."; fi

# Determine API Key
API_KEY="${argc_key:-${LINKEDIN_API_KEY:-}}"
if [[ -z "${API_KEY// /}" ]]; then
  die API key is required. Use -k/--key option or set LINKEDIN_API_KEY environment variable.
fi

# Determine API Host
if [[ -z "$argc_host" ]]; then
  # Use the default value if host parameter wasn't provided
  argc_host="linkedin-api8.p.rapidapi.com"
fi

# Rest of the script continues...

exit 0 # Exit successfully after command execution
