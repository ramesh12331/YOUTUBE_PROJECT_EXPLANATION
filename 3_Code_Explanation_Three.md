# 📘 YouTube Clone – Fully Commented Code Explanation (Components 4️⃣ to 🔟)

> This document contains **ONLY components 4 to 10**, explained in the **same fully commented, beginner-friendly + interview-ready style** as requested.

---

## ✅ 4. BODY COMPONENT – `Body.jsx`

### 📌 What this component does

* Defines the **main layout** of the application
* Keeps **Header and Sidebar persistent**
* Renders route-based content using `Outlet`

### 🔥 Fully Commented Code

```js
import React from "react";
import Sidebar from "./Sidebar";
import Head from "./Head";
import { Outlet } from "react-router-dom";

// Body is a layout component
// It wraps all pages with Header + Sidebar
const Body = () => {
  return (
    <>
      {/* Header is always visible */}
      <Head />

      {/* Main layout container */}
      <div className="flex gap-4 dark:bg-blue-300/20">

        {/* Sidebar visibility is controlled by Redux */}
        <Sidebar />

        {/* Outlet renders page based on current route */}
        {/* Home / Watch / Search */}
        <Outlet />
      </div>
    </>
  );
};

export default Body;
```

🧠 **Interview Summary**
“Body.jsx is a layout wrapper that enables nested routing while keeping the header and sidebar consistent.”

---

## ✅ 5. MAIN CONTAINER – `MainContainer.jsx`

### 📌 What this component does

* Represents the **Home Page container**
* Combines category buttons and video feed

### 🔥 Fully Commented Code

```js
import React from "react";
import ButtonList from "./ButtonList";
import VideoContainer from "./VideoContainer";

// MainContainer is the home page layout
const MainContainer = () => {
  return (
    <div className="flex-1 mt-14 pt-4 px-4 w-[83%]">

      {/* Category filter buttons */}
      <ButtonList />

      {/* Video listing section */}
      <VideoContainer />

    </div>
  );
};

export default MainContainer;
```

🧠 **Interview Summary**
“MainContainer follows the container pattern and composes the home page UI.”

---

## ✅ 6. BUTTON LIST – `ButtonList.jsx` & `Button.jsx`

### 📌 What these components do

* Render horizontal **category filters**
* Improve content discoverability

### 🔥 Button.jsx (Fully Commented)

```js
import React from "react";

function Button() {

  // Static list of categories
  const categories = [
    "All",
    "Music",
    "React",
    "Programming",
    "Cricket",
    "News",
    "Live",
  ];

  return (
    <div className="flex overflow-x-scroll scrollbar-hide px-4">
      <div className="flex space-x-4 flex-nowrap">

        {/* Render each category button */}
        {categories.map((category) => (
          <div
            key={category}
            className="bg-gray-200 dark:bg-green-700 rounded-xl px-4 py-2 text-xs font-semibold cursor-pointer"
          >
            {category}
          </div>
        ))}

      </div>
    </div>
  );
}

export default Button;
```

### 🔥 ButtonList.jsx

```js
import React from "react";
import Button from "./Button";

// Wrapper component for category buttons
const ButtonList = () => {
  return <Button />;
};

export default ButtonList;
```

🧠 **Interview Summary**
“These are stateless UI components focused purely on presentation.”

---

## ✅ 7. VIDEO CONTAINER – `VideoContainer.jsx`

### 📌 What this component does

* Fetches trending videos from YouTube API
* Stores them in local state
* Renders `VideoCard`

### 🔥 Fully Commented Code

```js
import React, { useEffect, useState } from "react";
import VideoCard from "./VideoCard";
import { YOUTUBE_VIDEO_API } from "../utils/constants";
import { Link } from "react-router-dom";

const VideoContainer = () => {

  // Local state to store videos
  const [videos, setVideos] = useState([]);

  // Fetch videos on component mount
  useEffect(() => {
    getVideos();
  }, []);

  // API call to YouTube
  const getVideos = async () => {
    const data = await fetch(YOUTUBE_VIDEO_API);
    const json = await data.json();
    setVideos(json.items);
  };

  return (
    <div className="flex max-h-screen overflow-y-scroll scrollbar-hide flex-wrap gap-4 pt-2 justify-center">

      {/* Render each video */}
      {videos.map((video) => (
        <Link key={video.id} to={`/watch?v=${video.id}`}>
          <VideoCard info={video} />
        </Link>
      ))}

    </div>
  );
};

export default VideoContainer;
```

🧠 **Interview Summary**
“VideoContainer separates API logic from UI rendering.”

---

## ✅ 8. VIDEO CARD – `VideoCard.jsx`

### 📌 What this component does

* Displays individual video information
* Pure presentational component

### 🔥 Fully Commented Code

```js
import React from "react";

const VideoCard = ({ info }) => {

  // Guard clause to prevent crashes
  if (!info || !info.snippet) return null;

  const { snippet, statistics } = info;
  const { title, thumbnails } = snippet;

  return (
    <div className="w-56 shadow-2xl hover:scale-105 transition-all">
      <img src={thumbnails.medium.url} alt="thumbnail" />

      <div className="p-2">
        <h2 className="font-bold truncate">{title}</h2>
        <p className="text-sm text-gray-500">Views: {statistics.viewCount}</p>
      </div>
    </div>
  );
};

export default VideoCard;
```

🧠 **Interview Summary**
“VideoCard is reusable, stateless, and follows single-responsibility.”

---

## ✅ 9. SEARCH RESULTS – `SearchResults.jsx`

### 📌 What this component does

* Displays results for searched videos
* Reads query from URL

### 🔥 Fully Commented Code

```js
import { Link, useSearchParams } from "react-router-dom";
import { useEffect, useState } from "react";
import VideoCardSearch from "./VideoCardSearch";

const SearchResults = () => {

  // Read query param from URL
  const [searchParams] = useSearchParams();
  const query = searchParams.get("q");

  const [videos, setVideos] = useState([]);

  // Fetch videos when query changes
  useEffect(() => {
    fetchVideos();
  }, [query]);

  const fetchVideos = async () => {
    const res = await fetch(`https://www.googleapis.com/youtube/v3/search?part=snippet&type=video&q=${query}`);
    const json = await res.json();
    setVideos(json.items || []);
  };

  return (
    <div className="flex flex-wrap gap-4 mt-14 w-[83%]">
      {videos
        .filter(v => v.id?.videoId)
        .map(video => (
          <Link key={video.id.videoId} to={`/watch?v=${video.id.videoId}`}>
            <VideoCardSearch info={video} />
          </Link>
        ))}
    </div>
  );
};

export default SearchResults;
```

🧠 **Interview Summary**
“SearchResults uses routing and API integration for dynamic search.”

---

## ✅ 🔟 WATCH PAGE – `WatchPage.jsx`

### 📌 What this component does

* Plays selected video
* Closes sidebar for focus

### 🔥 Fully Commented Code

```js
import React, { useEffect } from "react";
import { useDispatch } from "react-redux";
import { useSearchParams } from "react-router-dom";
import { closeMenu } from "../utils/appSlice";

const WatchPage = () => {

  const [searchParams] = useSearchParams();
  const dispatch = useDispatch();

  // Close sidebar when video opens
  useEffect(() => {
    dispatch(closeMenu());
  }, []);

  return (
    <div className="mt-14 w-[83%]">
      <iframe
        className="w-full h-[400px]"
        src={`https://www.youtube.com/embed/${searchParams.get("v")}`}
        allowFullScreen
      />
    </div>
  );
};

export default WatchPage;
```

🧠 **Interview Summary**
“WatchPage improves UX by focusing on video playback and controlling global UI state.”
