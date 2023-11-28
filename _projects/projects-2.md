---
title: "HackerNews Top Stories Fetcher Script"
excerpt: "This project showcases a Python script I developed to retrieve the top 10 stories from Hacker News for the current day. The script demonstrates proficiency in API integration, data processing, and presenting information in a readable format.<br/><img src='/images/hnscript500x300.png'>"
collection: projects
---


## Introduction
This project showcases a Python script I developed to retrieve the top 10 stories from Hacker News for the current day. The script demonstrates proficiency in API integration, data processing, and presenting information in a readable format.

## Problem Definition
The task was to create a script that:
- Interacts with the Hacker News API.
- Retrieves and sorts stories based on their popularity (score) for the current day.
- Handles exceptions and presents the data in an easy-to-read format.

## Development Process

### Converting Unix Timestamp to Datetime
The script begins with a utility function to convert Unix timestamps to Python datetime objects.

```
def convert_timestamp_to_date(timestamp):
    return datetime.fromtimestamp(timestamp, timezone.utc).date()
```
*This function is essential for interpreting the time data from Hacker News API responses.*

### Fetching Individual Item Details
The core of the script is fetching details for specific items from Hacker News.

```
def get_item_details(item_id):
    base_url = "https://hacker-news.firebaseio.com/v0/item/"
    try:
        response = requests.get(f"{base_url}{item_id}.json")
        response.raise_for_status()
        return response.json()
    except requests.RequestException as e:
        print(f"Error fetching item details: {e}")
        return None
```
*This function retrieves details for a given item, handling any request errors gracefully.*

### Fetching and Sorting Top Stories
The main functionality revolves around fetching the top stories for the current day and sorting them by score.

```
def get_top_stories():
    top_stories = []
    current_date = datetime.now(timezone.utc).date()

    try:
        response = requests.get("https://hacker-news.firebaseio.com/v0/topstories.json")
        response.raise_for_status()
        story_ids = response.json()

        for story_id in story_ids:
            if len(top_stories) >= 10:
                break  # Limit to top 10 stories

            item_details = get_item_details(story_id)
            if item_details and 'time' in item_details:
                story_date = convert_timestamp_to_date(item_details['time'])
                if story_date == current_date:
                    top_stories.append(item_details)

    except requests.RequestException as e:
        print(f"Error fetching top stories: {e}")

    return top_stories
```
*This function fetches the latest stories and filters them to find the top 10 for the current day.*

### Displaying the Stories
The script concludes with a function to neatly display each story's title, score, and Hacker News post URL.

```
def display_stories(stories):
    for idx, story in enumerate(stories, start=1):
        title = story.get('title', 'No Title')
        score = story.get('score', 'No Score')
        hn_url = f"https://news.ycombinator.com/item?id={story.get('id')}"
        print(f"{idx}. {title} (Score: {score})\nHacker News Post: {hn_url}\n")
```
*Formats and prints the top 10 stories, making them easy to read and understand.*

### Enhanced Story Fetching and Display
Finally, an enhanced function combines all previous functionalities, fetching and displaying the top stories.

```
def get_top_stories_corrected():
    try:
        top_stories = get_top_stories()
        if top_stories:
            sorted_stories = sorted(top_stories, key=lambda x: x.get('score', 0), reverse=True)
            display_stories(sorted_stories[:10])
        else:
            print("No stories found for the current day.")
    except Exception as e:
        print(f"An error occurred: {e}")
```
*This function serves as the script's entry point, orchestrating the fetching, sorting, and displaying of stories.*

### Full Script

```
import requests
from datetime import datetime, timezone


def convert_timestamp_to_date(timestamp):
    return datetime.fromtimestamp(timestamp, timezone.utc).date()


def get_item_details(item_id):
    base_url = "https://hacker-news.firebaseio.com/v0/item/"
    try:
        response = requests.get(f"{base_url}{item_id}.json")
        response.raise_for_status()
        return response.json()
    except requests.RequestException as e:
        print(f"Error fetching item details: {e}")
        return None


def get_top_stories():
    top_stories = []
    current_date = datetime.now(timezone.utc).date()

    try:
        response = requests.get("https://hacker-news.firebaseio.com/v0/topstories.json")
        response.raise_for_status()
        story_ids = response.json()

        for story_id in story_ids:
            if len(top_stories) >= 10:
                break  # Limit to top 10 stories

            item_details = get_item_details(story_id)
            if item_details and 'time' in item_details:
                story_date = convert_timestamp_to_date(item_details['time'])
                if story_date == current_date:
                    top_stories.append(item_details)

    except requests.RequestException as e:
        print(f"Error fetching top stories: {e}")

    return top_stories


def display_stories(stories):
    for idx, story in enumerate(stories, start=1):
        title = story.get('title', 'No Title')
        score = story.get('score', 'No Score')
        hn_url = f"https://news.ycombinator.com/item?id={story.get('id')}"
        print(f"{idx}. {title} (Score: {score})\nHacker News Post: {hn_url}\n")


def get_top_stories_corrected():
    try:
        top_stories = get_top_stories()
        if top_stories:
            sorted_stories = sorted(top_stories, key=lambda x: x.get('score', 0), reverse=True)
            display_stories(sorted_stories[:10])
        else:
            print("No stories found for the current day.")
    except Exception as e:
        print(f"An error occurred: {e}")

if __name__ == "__main__":
    print("Fetching top stories for the current day...\n")
    get_top_stories_corrected()

```

## Conclusion
This script effectively showcases my ability to work with external APIs, process data, and present it in a user-friendly manner. Future enhancements could include a web interface for broader accessibility.
