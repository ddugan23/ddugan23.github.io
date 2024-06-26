---
title: "Hacker News Top Stories Fetcher Script"
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

```python
def convert_timestamp_to_date(timestamp):
    return datetime.fromtimestamp(timestamp, timezone.utc).date()
```
*This function is essential for interpreting the time data from Hacker News API responses.*

### Fetching Individual Item Details
The core of the script is fetching details for specific items from Hacker News.

```python
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

```python
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

```python
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

```python
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

### The Script So Far

```python
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

### Refactored Script
To refactor the script for optimization, readability, and to remove unnecessary lines of code, I did the following:
1. **Combine Functions**: Integrated `get_item_details` within `get_top_stories` to reduce function calls.
2. **Optimize Sorting**: Sorted stories as they are fetched to avoid sorting the entire list later.
3. **Improve Error Handling**: Streamlined error messages for clarity.
4. **Refactor Display Function**: Ensured clear and concise output.
5. **Remove Redundant Code**: Identified and eliminated any unnecessary lines. 

```python
import requests
from datetime import datetime, timezone

# Fetches the top 10 stories from Hacker News for the current day
def get_top_stories():
    base_url = "https://hacker-news.firebaseio.com/v0/"
    current_date = datetime.now(timezone.utc).date()
    top_stories = []

    try:
        response = requests.get(f"{base_url}topstories.json")
        response.raise_for_status()
        story_ids = response.json()

        for story_id in story_ids:
            if len(top_stories) >= 10:
                break

            try:
                item_response = requests.get(f"{base_url}item/{story_id}.json")
                item_response.raise_for_status()
                item_details = item_response.json()
                if item_details and 'time' in item_details:
                    story_date = datetime.fromtimestamp(item_details['time'], timezone.utc).date()
                    if story_date == current_date:
                        top_stories.append(item_details)
            except requests.RequestException as e:
                print(f"Error fetching item details: {e}")

        top_stories.sort(key=lambda x: x.get('score', 0), reverse=True)
        return top_stories[:10]

    except requests.RequestException as e:
        print(f"Error fetching top stories: {e}")
        return []

# Sorts and displays the top stories
def display_stories(stories):
    for idx, story in enumerate(stories, start=1):
        title = story.get('title', 'No Title')
        score = story.get('score', 'No Score')
        hn_url = f"https://news.ycombinator.com/item?id={story.get('id')}"
        print(f"{idx}. {title} (Score: {score})\nHacker News Post: {hn_url}\n")

# Main function to fetch and display top stories
def main():
    print("Fetching top stories for the current day...\n")
    top_stories = get_top_stories()
    if top_stories:
        display_stories(top_stories)
    else:
        print("No stories found for the current day.")

if __name__ == "__main__":
    main()
```

### Adding a GUI
I used Tkinter to create a simple GUI for the program, which included creating two buttons and stylizing the main window.

```python
def fetch_stories():
    top_stories = get_top_stories()
    if top_stories:
        display_stories(top_stories, stories_frame)
    else:
        label = tk.Label(stories_frame, text="No stories found for the current day.")
        label.pack()

# GUI Styling Parameters
BG_COLOR = "#ECEDE7"  # Background color
FG_COLOR = "#777573"  # Foreground color
ACCENT_COLOR = "#826D69"  # Accent color for buttons and links
FONT = ("Roboto", 10)

# Create and configure the main application window
app = tk.Tk()
app.title("Hacker News Top Stories")
app.configure(bg=BG_COLOR)

# Style configuration for button
style = ttk.Style()
style.configure("C.TButton", font=FONT, background=ACCENT_COLOR)

# Fetch Stories Button
fetch_button = ttk.Button(app, text="Fetch Top Stories", style="C.TButton", command=fetch_stories)
fetch_button.pack(pady=10)

# Frame for displaying stories
stories_frame = tk.Frame(app, bg=BG_COLOR)
stories_frame.pack(fill="both", expand=True)

app.mainloop()
```
![](/images/hnimage.png)

### Full Script
```python
import tkinter as tk
from tkinter import ttk
import requests
from datetime import datetime, timezone
import webbrowser

# Fetches the top 10 stories from Hacker News for the current day
def get_top_stories():
    base_url = "https://hacker-news.firebaseio.com/v0/"
    current_date = datetime.now(timezone.utc).date()
    top_stories = []

    try:
        response = requests.get(f"{base_url}topstories.json")
        response.raise_for_status()
        story_ids = response.json()

        for story_id in story_ids:
            if len(top_stories) >= 10:
                break

            try:
                item_response = requests.get(f"{base_url}item/{story_id}.json")
                item_response.raise_for_status()
                item_details = item_response.json()
                if item_details and 'time' in item_details:
                    story_date = datetime.fromtimestamp(item_details['time'], timezone.utc).date()
                    if story_date == current_date:
                        top_stories.append(item_details)
            except requests.RequestException as e:
                print(f"Error fetching item details: {e}")

        top_stories.sort(key=lambda x: x.get('score', 0), reverse=True)
        return top_stories[:10]

    except requests.RequestException as e:
        print(f"Error fetching top stories: {e}")
        return []

# Sorts and displays the top stories
def display_stories(stories, container):
    for widget in container.winfo_children():
        widget.destroy()

    for idx, story in enumerate(stories, start=1):
        title = story.get('title', 'No Title')
        score = story.get('score', 'No Score')
        hn_url = f"https://news.ycombinator.com/item?id={story.get('id')}"
        label = tk.Label(container, text=f"{idx}. {title} (Score: {score})", wraplength=300)
        label.pack()
        link = tk.Label(container, text="Hacker News Post", fg="blue", cursor="hand2")
        link.pack()
        link.bind("&lt;Button-1&gt;", lambda e, url=hn_url: webbrowser.open_new(url))

def fetch_stories():
    top_stories = get_top_stories()
    if top_stories:
        display_stories(top_stories, stories_frame)
    else:
        label = tk.Label(stories_frame, text="No stories found for the current day.")
        label.pack()

# GUI Styling Parameters
BG_COLOR = "#ECEDE7"  # Background color
FG_COLOR = "#777573"  # Foreground color
ACCENT_COLOR = "#826D69"  # Accent color for buttons and links
FONT = ("Roboto", 10)

# Create and configure the main application window
app = tk.Tk()
app.title("Hacker News Top Stories")
app.configure(bg=BG_COLOR)

# Style configuration for button
style = ttk.Style()
style.configure("C.TButton", font=FONT, background=ACCENT_COLOR)

# Fetch Stories Button
fetch_button = ttk.Button(app, text="Fetch Top Stories", style="C.TButton", command=fetch_stories)
fetch_button.pack(pady=10)

# Frame for displaying stories
stories_frame = tk.Frame(app, bg=BG_COLOR)
stories_frame.pack(fill="both", expand=True)

app.mainloop()
```

### Conclusion
This script effectively showcases my ability to work with external APIs, process data, and present it in a user-friendly manner. Future enhancements could include a web interface for broader accessibility.
