# 📘 YouTube Clone – Fully Commented, Beginner-Friendly & Interview-Ready README

> ⚠️ **Note:** The content below is presented **exactly as provided**, without modification. It is structured only for README / Canva usage.

---

## Below is a FULLY COMMENTED, BEGINNER-FRIENDLY + INTERVIEW-READY EXPLANATION of your code.

Everything is explained:

* What each file does
* Why each line exists
* Inline comments for every important part
* Extra interview points

---

## ✅ 1. THEME CONTEXT – `contexts/theme.js`

This file controls Dark / Light mode globally using Context API.

### 🔥 Fully Commented Code

```js
import { createContext, useContext } from "react";


// -------------------------------
// Create Theme Context
// -------------------------------
// This context stores:
//  - current theme (light/dark)
//  - functions to switch theme
// Default values prevent undefined access errors
export const ThemeContext = createContext({
    themeMode: "light",     // default theme
    darkTheme: () => {},    // empty default function
    lightTheme: () => {},   // empty default function
});


// -------------------------------
// Provider Component
// -------------------------------
// We will wrap App.jsx with this provider
export const ThemeProvider = ThemeContext.Provider;


// -------------------------------
// Custom Hook for easy usage
// -------------------------------
// Instead of: useContext(ThemeContext)
// We simply use: useTheme()
export default function useTheme() {
    return useContext(ThemeContext);
}
```

### 🧠 Interview Explanation

> “I used Context API for theme because it is UI-only state. Using Redux for this would add unnecessary complexity. The custom hook makes consuming the theme simple.”

---

## ✅ 2. HEADER COMPONENT – `Head.jsx`

This is the most complex UI component.

### Handles:

* Menu toggle
* Search input
* Search suggestions (debounced + cached)
* Navigation
* Theme toggle

### 🔥 Fully Commented Code

```js
import React, { useEffect, useState } from "react";
import { AiOutlineMenu, AiOutlineBell } from "react-icons/ai";
import { CiSearch } from "react-icons/ci";
import { IoMdMic } from "react-icons/io";
import { RiVideoAddLine } from "react-icons/ri";

import { toggleMenu } from "../utils/appSlice";
import { useDispatch, useSelector } from "react-redux";

import ThemeToggleButton from "./ThemeToggleButton";
import { YOUTUBE_SEARCH_API } from "../utils/constants";
import { IoSearch } from "react-icons/io5";
import { Link, useNavigate } from "react-router-dom";
import { cacheResults } from "../utils/searchSlice";


const Head = () => {

  // -------------------------------
  // Local State for Search Feature
  // -------------------------------
  const [searchQuery, setSearchQuery] = useState("");     // what user types
  const [suggestion, setSuggestion] = useState([]);       // suggestions list
  const [showSuggestions, setShowSuggestions] = useState(false);


  // -------------------------------
  // Access Redux Store
  // -------------------------------
  const searchCache = useSelector((store) => store.search);   // cached queries
  const dispatch = useDispatch();
  const navigate = useNavigate();


  // -------------------------------
  // Debounced Suggestions Logic
  // -------------------------------
  useEffect(() => {

    // Wait 200ms after user stops typing
    const timer = setTimeout(() => {

      // If search query is already cached → use cache
      if (searchCache[searchQuery]) {
        setSuggestion(searchCache[searchQuery]);
      }
      // Else → fetch from API
      else {
        getSearchSuggestions();
      }

    }, 200);

    // Cleanup timer on every keystroke
    return () => clearTimeout(timer);

  }, [searchQuery]);


  // -------------------------------
  // API Call to get suggestions
  // -------------------------------
  const getSearchSuggestions = async () => {
    try {
      const data = await fetch(YOUTUBE_SEARCH_API + searchQuery);
      const json = await data.json();

      // json[1] contains suggestion array
      setSuggestion(json[1]);

      // update Redux cache
      dispatch(cacheResults({ [searchQuery]: json[1] }));

    } catch (err) {
      console.error("Error fetching suggestions:", err);
    }
  };


  // -------------------------------
  // Hamburger Menu Toggle
  // -------------------------------
  const toggleMenuHandler = () => {
    dispatch(toggleMenu());
  };


  // -------------------------------
  // Navigate on Search
  // -------------------------------
  const handleSearch = () => {
    if (searchQuery.trim()) {
      navigate(`/search?q=${searchQuery}`);
      setShowSuggestions(false);
    }
  };



  // ------------------------------------------------------
  // JSX UI (Header Layout: Left → Center → Right)
  // ------------------------------------------------------
  return (
    <div className="flex justify-between px-6 py-2 bg-white shadow-lg dark:bg-gray-900 text-black dark:text-white fixed top-0 left-0 right-0 z-50">

      {/* ------------------ LEFT: Logo + Menu -------------------------- */}
      <div className="flex items-center space-x-4">

        {/* Hamburger icon */}
        <AiOutlineMenu
          onClick={toggleMenuHandler}
          className="text-xl cursor-pointer"
        />

        {/* Logo */}
        <Link to="/">
          <img className="w-26 rounded-lg" src="./logo.png" alt="Logo" />
        </Link>

      </div>

      {/* ------------------ CENTER: Search Bar -------------------------- */}
      <div className="flex w-full md:max-w-[600px] mx-4 relative">

        {/* Search input */}
        <div className="w-full px-3 py-2 border rounded-l-full bg-white dark:bg-gray-700">
          <input
            type="text"
            placeholder="Search"
            className="outline-none bg-transparent w-full"
            value={searchQuery}
            onChange={(e) => setSearchQuery(e.target.value)}
            onFocus={() => setShowSuggestions(true)}

            // hide suggestion dropdown after clicking
            onBlur={() => setTimeout(() => setShowSuggestions(false), 100)}
          />
        </div>

        {/* Search button */}
        <button
          className="px-4 py-2 border rounded-r-full"
          onClick={handleSearch}
        >
          <CiSearch className="font-extrabold cursor-pointer" />
        </button>


        {/* ------------------ SUGGESTIONS DROPDOWN ---------------------- */}
        {showSuggestions && suggestion.length > 0 && (
          <ul className="absolute top-full left-0 w-full bg-white dark:bg-gray-800 shadow-lg rounded-lg mt-1 z-50">

            {suggestion.map((s) => (
              <li
                key={s}
                className="flex items-center gap-2 p-2 hover:bg-gray-100 dark:hover:bg-gray-700 cursor-pointer"

                // onMouseDown prevents input blur
                onMouseDown={() => navigate(`/search?q=${s}`)}
              >
                <IoSearch /> {s}
              </li>
            ))}

          </ul>
        )}

        {/* Mic Icon */}
        <IoMdMic
          size={"42px"}
          className="ml-3 rounded-full p-2 cursor-pointer hover:bg-gray-200 dark:hover:bg-gray-700 duration-200 hidden md:flex"
        />

      </div>


      {/* ------------------ RIGHT: Icons + Theme Toggle ---------------- */}
      <div className="flex space-x-5 items-center">
        <RiVideoAddLine className="text-2xl hidden md:block cursor-pointer" />
        <AiOutlineBell className="text-2xl hidden md:block cursor-pointer" />

        {/* Dummy Profile Icon */}
        <img
          className="w-6 rounded-full hidden md:flex cursor-pointer"
          src="https://static.thenounproject.com/png/1122811-200.png"
          alt="User Avatar"
        />

        {/* THEME TOGGLE */}
        <ThemeToggleButton />
      </div>

    </div>
  );
};


export default Head;
```

### 🧠 INTERVIEW SUMMARY

> “The Head component contains all top navigation logic — debounced search with caching, menu toggle using Redux, theme toggle using Context, and navigation using React Router. It's the most interactive component in the project.”

---

## ✅ 3. SIDEBAR COMPONENT – `Sidebar.jsx`

This component uses Redux to show/hide Sidebar.

### 🔥 Fully Commented Code

```js
import React from "react";
import { GoHome } from "react-icons/go";
import { SiYoutubeshorts } from "react-icons/si";
import { MdOutlineSubscriptions, MdHistory } from "react-icons/md";
import { useSelector } from "react-redux";
import { Link } from "react-router-dom";


function Sidebar() {

  // Read sidebar open/close value from Redux
  const isMenuOpen = useSelector((store) => store.app.isMenuOpen);


  // -----------------------------------------
  // EARLY RETURN
  // If isMenuOpen is false → DO NOT render Sidebar
  // This improves performance
  // -----------------------------------------
  if (!isMenuOpen) return null;


  // Sidebar navigation items (DATA DRIVEN UI)
  const sidebarItems = [
    { id: 1, name: "Home", to: "/", icon: <GoHome /> },
    { id: 2, name: "Shorts", icon: <SiYoutubeshorts /> },
    { id: 3, name: "Subscriptions", icon: <MdOutlineSubscriptions /> },
  ];


  return (
    <div className="px-6 w-[17%] max-h-screen overflow-y-scroll mt-14">

      {/* ------------ SECTION 1: Home Navigation ------------- */}
      <div className="space-y-3">

        {sidebarItems.map((item) => (
          <div
            key={item.id}
            className="flex items-center space-x-6 hover:bg-gray-300 rounded-xl p-1"
          >
            <div className="text-xl cursor-pointer">
              <Link to={item.to}>{item.icon}</Link>
            </div>

            <span className="cursor-pointer">
              <Link to={item.to}>{item.name}</Link>
            </span>

          </div>
        ))}

      </div>

    </div>
  );
}


export default Sidebar;
```

### 🧠 INTERVIEW SUMMARY

> “Sidebar uses Redux Early Return pattern, making the UI more optimized. It is fully data-driven and renders navigation items dynamically.”
