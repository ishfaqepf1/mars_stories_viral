import streamlit as st

import requests

from datetime import datetime, timedelta


# YouTube API Key

API_KEY = "AIzaSyDIE2FYZ15w5lOYAGECyz1gs7qhedKXi6g"

YOUTUBE_SEARCH_URL = "https://www.googleapis.com/youtube/v3/search"

YOUTUBE_VIDEO_URL = "https://www.googleapis.com/youtube/v3/videos"

YOUTUBE_CHANNEL_URL = "https://www.googleapis.com/youtube/v3/channels"


# Streamlit App Title

st.title("YouTube Viral Topics Tool")


# Input Fields

days = st.number_input("Enter Days to Search (1-30):", min_value=1, max_value=30, value=5)


# List of broader keywords

keywords = [

"Mars", "SpaceX", "Colonizing Mars", "NASA Mars", "Mars Sample Return", "Terraforming Mars",
"Can we colonize Mars", "How to colonize Mars", "Is Mars safe", "Life on Mars", "Living on Mars",
"Mars 2050", "SpaceX Mars", "Mars colonization", "Mars exploration", "Why go to Mars", "Mars settlement",
"Human missions to Mars", "Reasons to go to Mars", "Reasons not to go to Mars", "Future of space travel",
"SpaceX Mars mission", "Martian colony", "Moon vs Mars", "Humanity in space",
"Science and Futurism with Isaac Arthur", "Elon Musk", "Department of Government Efficiency",
"Lockheed Martin", "Mars Desert Research Station", "Space exploration", "Space Launch System",
"Space Shuttle", "International Space Station", "Cosmic radiation", "Rocket propellant",
"Sending humans to Mars", "NASA discovery", "Alien life", "Mars rover", "Mars mission",
"Going to Mars", "How will we go to Mars", "Space documentary", "SpaceX Starship", "Space science",
"Deep space travel", "Orbital refueling", "Space radiation", "Mars landing", "Supersonic retropropulsion",
"Science explained", "Futurism", "Space engineering", "Astronomy documentary", "Space psychology",
"Journey of a Visionary", "Why we won’t go to Mars", "NASA secrets", "Astronomy", "Solar System",
"Space missions", "Mars atmosphere", "Will we colonize Titan", "Mars civilization", "Humans in space",
"Rocket spaceship", "Space technology", "Growing food on Mars", "Mars farming", "Mars radiation",
"Space farming", "Water on Mars", "Interesting engineering", "Human body in space",
"How does space change a human body", "Impacts of space", "How does microgravity impact a body",
"Microgravity", "When are we going to Mars", "The problems with going to Mars",
"Technical challenges with going to Mars", "Next Mars mission", "Next space mission", "migration mars", migration to mars",
"why we migrated to mars", "why we left earth", "life on mars"

]


# Fetch Data Button

if st.button("Fetch Data"):

    try:

        # Calculate date range

        start_date = (datetime.utcnow() - timedelta(days=int(days))).isoformat("T") + "Z"

        all_results = []


        # Iterate over the list of keywords

        for keyword in keywords:

            st.write(f"Searching for keyword: {keyword}")


            # Define search parameters

            search_params = {

                "part": "snippet",

                "q": keyword,

                "type": "video",

                "order": "viewCount",

                "publishedAfter": start_date,

                "maxResults": 5,

                "key": API_KEY,

            }


            # Fetch video data

            response = requests.get(YOUTUBE_SEARCH_URL, params=search_params)

            data = response.json()


            # Check if "items" key exists

            if "items" not in data or not data["items"]:

                st.warning(f"No videos found for keyword: {keyword}")

                continue


            videos = data["items"]

            video_ids = [video["id"]["videoId"] for video in videos if "id" in video and "videoId" in video["id"]]

            channel_ids = [video["snippet"]["channelId"] for video in videos if "snippet" in video and "channelId" in video["snippet"]]


            if not video_ids or not channel_ids:

                st.warning(f"Skipping keyword: {keyword} due to missing video/channel data.")

                continue


            # Fetch video statistics

            stats_params = {"part": "statistics", "id": ",".join(video_ids), "key": API_KEY}

            stats_response = requests.get(YOUTUBE_VIDEO_URL, params=stats_params)

            stats_data = stats_response.json()


            if "items" not in stats_data or not stats_data["items"]:

                st.warning(f"Failed to fetch video statistics for keyword: {keyword}")

                continue


            # Fetch channel statistics

            channel_params = {"part": "statistics", "id": ",".join(channel_ids), "key": API_KEY}

            channel_response = requests.get(YOUTUBE_CHANNEL_URL, params=channel_params)

            channel_data = channel_response.json()


            if "items" not in channel_data or not channel_data["items"]:

                st.warning(f"Failed to fetch channel statistics for keyword: {keyword}")

                continue


            stats = stats_data["items"]

            channels = channel_data["items"]


            # Collect results

            for video, stat, channel in zip(videos, stats, channels):

                title = video["snippet"].get("title", "N/A")

                description = video["snippet"].get("description", "")[:200]

                video_url = f"https://www.youtube.com/watch?v={video['id']['videoId']}"

                views = int(stat["statistics"].get("viewCount", 0))

                subs = int(channel["statistics"].get("subscriberCount", 0))


                if subs < 3000:  # Only include channels with fewer than 3,000 subscribers

                    all_results.append({

                        "Title": title,

                        "Description": description,

                        "URL": video_url,

                        "Views": views,

                        "Subscribers": subs

                    })


        # Display results

        if all_results:

            st.success(f"Found {len(all_results)} results across all keywords!")

            for result in all_results:

                st.markdown(

                    f"**Title:** {result['Title']}  \n"

                    f"**Description:** {result['Description']}  \n"

                    f"**URL:** [Watch Video]({result['URL']})  \n"

                    f"**Views:** {result['Views']}  \n"

                    f"**Subscribers:** {result['Subscribers']}"

                )

                st.write("---")

        else:

            st.warning("No results found for channels with fewer than 3,000 subscribers.")


    except Exception as e:

        st.error(f"An error occurred: {e}")


