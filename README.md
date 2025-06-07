# Hotspot Design

This repository describes the application architecture for the Hotspot project

## About

The HotSpot project is an open source project meant to provide a simple and easy way for
people to see where they and others are spending their time. With enough adoption, this project could potentially:

- Uncover local attractions that you might want to visit
- Reveal common traffic jams in your area
- Help you remember that one place you went to last year but forgot the name of

## Components

The project is divided into a couple of main components:

- A mobile application:
  - tracks your location when you're not at home
  - shows your activity and that of your friends
  - displays other insights
- A backend server:
  - processes location data from the mobile application
  - generates reports and provides meaningful insights.

More information about the specific components can be found in their respective Markdown files.

## Requirements

The application must be fast enough to provide real-time insights on:

- what spaces are currently packed

The application must provide insights on:

- where you spend your time
- how many people were at a location at a specific time
- what locations are popular
