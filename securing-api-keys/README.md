# ![[tktk Module Name] - tktk Microlesson Name](./assets/hero.png)

**Learning objective:** By the end of this lesson, students will be able to tktk

This is absolutely a level-up opportunity for SEB, but this should address the concept that some APIs require an API key, and since react is on the front-end, whoops, absoluteley nothing is secure here, so we should NOT be hardcoding API keys into the headers of our fetch requests.

Instead, set up a little proxy express server that has a route for the API request we want, where we can safely hide the API key in environment variables stored on a backend server.