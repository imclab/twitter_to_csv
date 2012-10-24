# Twitter To CSV

A tool for exporting the Twitter stream into a CSV file.

    (sudo) gem install twitter_to_csv

## Usage

You might start by first running the script for a while to dump the Twitter stream into a JSON file:

    twitter_to_csv --username <your twitter username> --password <your twitter password> \
                   --json out.json --filter your,keywords,of,interest

Then, later, you could export to CSV:

    twitter_to_csv --replay-from-file out.json --csv out.csv \
                   --fields text,created_at,user.name,retweeted_status.id,retweeted_status.favorited,...

Alternatively, you can always stream directly to CSV:

    twitter_to_csv --username <your twitter username> --password <your twitter password> \
                   --filter your,keywords,of,interest --csv out.csv \
                   --fields text,created_at,user.name,retweeted_status.id,retweeted_status.favorited,...

## Requiring English

You may want to limit to Tweets that appear to be written in English.

    twitter_to_csv --username <your twitter username> --password <your twitter password> \
                   --require-english --fields ...

This filter isn't perfect and will have both false positives and false negatives, but it works fairly well.

## URLS, Hashtags, and User Mentions

You can extract URLs, Hashtags, and User Mentions from the tweet into their own columns by using `--url-columns`, `--hash-columns`, and `--user-mention-columns`.
For example, you could use `--url-columns 3` to get up to 3 extracted URLs in their own columns.

## Sentiment Tagging

Twitter To CSV can compute an average sentiment score for each tweet.  Provide `--compute-sentiment` to use this feature.
The [AFINN-111](http://fnielsen.posterous.com/old-anew-a-sentiment-about-sentiment-analysis) valence database is used to look up the valence of
each recognized word, then the average is computed, only considering words that have some known valence associated.  That is, "I love cheese" only has
one word with valence, "love" with a score of 3, so the average is 3.  "I love cheese and like bread", on the other hand, has two words with
valence, "love" (3) and "like" (2), and so has an average valence of (3 + 2) / 2, or 2.5.  The library will break hyphenated words up and score them as
separate words unless the whole thing has a single known valence.

## Handling of Retweets

Once you have a recorded Twitter stream, you can rollup retweets in various ways.  Here is an example that collapses retweets into the `retweet_count` field of the original tweet, only outputs tweets with at least 1 retweet, ignores retweets that happened more than 7 days after the original tweet, and outputs retweet count columns at half an hour, 2 hours, and 2 days after the original tweet:

    twitter_to_csv --replay-from-file out.json -c out.csv \
                   --retweet-mode rollup \
                   --retweet-threshold 1 \
                   --retweet-window 7 \
                   --retweet-counts-at 0.5,2,48 \
                   --fields retweet_count,text

Note that all of the retweet features require you to `--replay-from-file` because they parse the stream backwards.  They will not function correctly from the stream directly.

## Selecting Windows

To select a specific window of time in a pre-recorded stream by `created_at`, pass in `--start` and `--end`, for example:

    twitter_to_csv --replay-from-file out.json \
                   --start "Mon Mar 07 07:42:22 +0000 2011" \
                   --end "Mon Mar 08 07:42:22 +0000 2011" \
                   ...

## Mind the Gap

Sometimes the Twitter API goes down.  You can analyze a json output file to see where data gaps (of over 10 minutes, in this case) have occurred:

    twitter_to_csv --replay-from-file out.json --analyze-gaps 10

## Field names

Use `--sample-fields 1000` to output the occurrence count of different Twitter fields, like so:

    twitter_to_csv --username <your twitter username> --password <your twitter password> --sample-fields 1000

Here's a partial list:

    in_reply_to_screen_name
    favorited
    text
    entities.urls
    entities.user_mentions
    entities.hashtags
    in_reply_to_user_id
    contributors
    place
    coordinates
    source
    geo
    retweeted
    retweet_count
    in_reply_to_status_id
    in_reply_to_status_id_str
    id_str
    user.default_profile_image
    user.verified
    user.notifications
    user.profile_sidebar_border_color
    user.screen_name
    user.lang
    user.favourites_count
    user.contributors_enabled
    user.profile_use_background_image
    user.friends_count
    user.location
    user.profile_text_color
    user.followers_count
    user.profile_image_url
    user.description
    user.statuses_count
    user.following
    user.profile_background_image_url
    user.show_all_inline_media
    user.listed_count
    user.profile_link_color
    user.is_translator
    user.default_profile
    user.time_zone
    user.profile_background_color
    user.protected
    user.id_str
    user.geo_enabled
    user.profile_background_tile
    user.name
    user.profile_background_image_url_https
    user.created_at
    user.profile_sidebar_fill_color
    user.id
    user.follow_request_sent
    user.utc_offset
    user.url
    user.profile_image_url_https
    truncated
    id
    created_at
    in_reply_to_user_id_str
    retweeted_status.in_reply_to_screen_name
    retweeted_status.favorited
    retweeted_status.text
    retweeted_status.entities.urls
    retweeted_status.entities.user_mentions
    retweeted_status.entities.hashtags
    retweeted_status.in_reply_to_user_id
    retweeted_status.contributors
    retweeted_status.place
    retweeted_status.coordinates
    retweeted_status.source
    retweeted_status.geo
    retweeted_status.retweeted
    retweeted_status.retweet_count
    retweeted_status.in_reply_to_status_id
    retweeted_status.in_reply_to_status_id_str
    retweeted_status.id_str
    retweeted_status.user.default_profile_image
    retweeted_status.user.verified
    retweeted_status.user.notifications
    retweeted_status.user.profile_sidebar_border_color
    retweeted_status.user.screen_name
    retweeted_status.user.lang
    retweeted_status.user.favourites_count
    retweeted_status.user.contributors_enabled
    retweeted_status.user.profile_use_background_image
    retweeted_status.user.friends_count
    retweeted_status.user.location
    retweeted_status.user.profile_text_color
    retweeted_status.user.followers_count
    retweeted_status.user.profile_image_url
    retweeted_status.user.description
    retweeted_status.user.statuses_count
    retweeted_status.user.following
    retweeted_status.user.profile_background_image_url
    retweeted_status.user.show_all_inline_media
    retweeted_status.user.listed_count
    retweeted_status.user.profile_link_color
    retweeted_status.user.is_translator
    retweeted_status.user.default_profile
    retweeted_status.user.time_zone
    retweeted_status.user.profile_background_color
    retweeted_status.user.protected
    retweeted_status.user.id_str
    retweeted_status.user.geo_enabled
    retweeted_status.user.profile_background_tile
    retweeted_status.user.name
    retweeted_status.user.profile_background_image_url_https
    retweeted_status.user.created_at
    retweeted_status.user.profile_sidebar_fill_color
    retweeted_status.user.id
    retweeted_status.user.follow_request_sent
    retweeted_status.user.utc_offset
    retweeted_status.user.url
    retweeted_status.user.profile_image_url_https
    retweeted_status.truncated
    retweeted_status.id
    retweeted_status.created_at
    retweeted_status.in_reply_to_user_id_str
    possibly_sensitive
    possibly_sensitive_editable
    retweeted_status.possibly_sensitive
    retweeted_status.possibly_sensitive_editable
    place.country_code
    place.place_type
    place.country
    place.bounding_box.type
    place.bounding_box.coordinates
    place.full_name
    place.name
    place.id
    place.url
    coordinates.type
    coordinates.coordinates
    geo.type
    geo.coordinates
    retweeted_status.entities.media
    entities.media
