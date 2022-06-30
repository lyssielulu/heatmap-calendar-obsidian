# Heatmap Calendar plugin for Obsidian

Visualise your data in a full year heatmap calendar similar to the github activity calendar using this [Obsidian](https://obsidian.md/) plugin.  

Useful for tracking your progress for various things such as exercise, finance, passion, vice, social, project progression etc.   

Tracking and visualizing the data can help motivate you to start doing the things you want to do, or even more – motivate you to continue once you've started!

It's intended to be used alongside [DataviewJS](https://blacksmithgu.github.io/obsidian-dataview/), but could be used standalone or with other plugins as well, as all the plugin does, is add the function ***renderHeatmapCalendar()*** to the global namespace.

<p align="center">
    <picture>
      <source media="(prefers-color-scheme: dark)" srcset="https://github.com/Richardsl/heatmap-calendar-obsidian/blob/master/github-images/heatmap_examples_dark.gif?raw=true">
      <source media="(prefers-color-scheme: light)" srcset="https://github.com/Richardsl/heatmap-calendar-obsidian/blob/master/github-images/heatmap_examples_light.gif?raw=true">
      <img alt="Shows a black logo in light color mode and a white one in dark color mode." src="https://user-images.githubusercontent.com/25423296/163456779-a8556205-d0a5-45e2-ac17-42d089e3c3f8.png">
    </picture>
</p>

## Use:

1. Annotate the data you want to track in your daily notes (see [Dataview annotation documentation](https://blacksmithgu.github.io/obsidian-dataview/data-annotation/)) 
2. Create a [DataviewJS block](https://blacksmithgu.github.io/obsidian-dataview/api/intro/) wherever you want the Heatmap Calendar to display.  
3. Collect the data you want to display using [DataviewJS](https://blacksmithgu.github.io/obsidian-dataview/api/code-reference/)
4. Pass the data into Heatmap Calendar using  **renderHeatmapCalendar()** 

![heatmap calendar example](https://github.com/Richardsl/heatmap-calendar-obsidian/blob/master/github-images/heatmap-calendar-howto3.jpg?raw=true)


## Full Example Code:

~~~javascript
\```dataviewjs
const calendarData = { 
	year: 2022,  // (optional) Defaults to current year
	colors: {    // (optional) Defaults to green
	  blue:        ["#8cb9ff","#69a3ff","#428bff","#1872ff","#0058e2"], // first entry is considered default if supplied
	  green:       ["#c6e48b","#7bc96f","#49af5d","#2e8840","#196127"],
	  red:         ["#ff9e82","#ff7b55","#ff4d1a","#e73400","#bd2a00"],
	  orange:      ["#ffa244","#fd7f00","#dd6f00","#bf6000","#9b4e00"],
	  pink:        ["#ff96cb","#ff70b8","#ff3a9d","#ee0077","#c30062"],
	  orangeToRed: ["#ffdf04","#ffbe04","#ff9a03","#ff6d02","#ff2c01"]
	},
	showCurrentDayBorder: true, // (optional) Defaults to true
	defaultEntryIntensity: 4, // (optional) Defaults to 4
	intensityScaleStart: 10, // (optional) Defaults to lowest value passe to entries.intensity
    intensityScaleEnd: 100, // (optional) Defaults to highest value passe to entries.intensity
	entries: [], // Populated in the DataviewJS loop below
}

//DataviewJS loop
for(let page of dv.pages('"daily notes"').where(p=>p.exercise).sort(p=>p.file.name)){ 

	calendarData.entries.push({
		date: page.file.name, // (required) Format YYYY-MM-DD
		intensity: page.exercise, // (required) Color intensity for entry, will map intensities automatically
		content: "🏋️", // (optional) Add text to the date cell
		color: "orange", // (optional) Reference from *calendarData.colors*. If no color is supplied; colors[0] is used
	})

}

/**
* param1  HTMLElement   DOM reference for calendar rendering
* param2  CalendarData  Calendar data object from above
*/
renderHeatmapCalendar(this.container, calendarData)
```
~~~

  
## Colors:
You don't need to supply any colors, the calendar uses green by default, just like Github.   
If you do supply colors to *calendarData.colors*, the first index will be considered the new default color.

Add a custom color to each entry by specifying the name you gave the color in calendarData.colors.
You can even use multiple colors in the same calendar, just use different colors for different entries.  
   
The color schemes used in the examples were created at [leonardocolor.io](https://leonardocolor.io).

## Intensity:
The "Intensity" means which intensity of color to use, for example from light-green to dark-green, 
and they will be distributed between the highest and lowest number you pass to "intensity".  
If the number range 0-100 is used, numbers between 1-20 would map to the lightest color, 40-60 would map to mid intensity color, and 80-100 would map to max intensity.
You can add more intensities in order to increase color resolution; simply supply more colors to **calendarData.colors.yourcolor**

Dataview's time variables are supported without any conversion, as they return milliseconds by default.  
**[time:: 1 hours, 35 minutes] => intensity: page.time**

## Styling:
Use Obsidian CSS snippets for custom styling.  
See [snippet examples](https://github.com/Richardsl/heatmap-calendar-obsidian/tree/master/EXAMPLE_VAULT/.obsidian/snippets).

## Notes:
- See the [EXAMPLE VAULT](https://github.com/Richardsl/heatmap-calendar-obsidian/tree/master/EXAMPLE_VAULT) if you want to test out the examples.
- Week starts on Monday, not configurable yet
- Date format is YYYY-MM-DD, if your daily note filename is something else, [you can use JS to change it in the loop](https://github.com/Richardsl/heatmap-calendar-obsidian/discussions/2)

## Development (Windows):
 ```npm run dev``` - will start TS to JS transpiler and automatically copy the JS/CSS/manifest files to the example vault whenever they are modified.  
 Installing https://github.com/pjeby/hot-reload is recommended to avoid restarting obsidian after every change, but remember to add a **.hot-reload** file to EXAMPLE_VAULT/.obsidian/plugins/heatmap-calendar/

---

### Changelog:
#### [0.4.0] - 2022-06-25

##### Feature
- Add hover preview feature courtesy of @arsenty from issue #12.  
to enable - add **content: await dv.span(`[](${page.file.name})`)** to entries, and enable **Settings** -> **Core Plugins** -> **Page Preview**.   
Optionally install plugin [Metatable](https://github.com/arnau/obsidian-metatable) to display metadata/frontmatter in the preview window aswell.  
See examples for more details

#### [0.3.0] - 2022-06-25

##### Feature
- Can add more intensities in order to increase color resolution. simply supply more colors to **calendarData.colors.yourcolor**
- Can set custom range on the intensity scaling using *intensityScaleStart* and *intensityScaleEnd*

##### Bugfix
- fix bug where entries from other years showing up in the calendar


#### [0.2.0] - 2022-06-05

##### Feature
- Add border around todays box to indicate what day it is. Can be removed by setting *showCurrentDayBorder* to false
- Add better development solution/workflow by using automated file copying instead of symlinks


#### [0.1.1] - 2022-03-18

##### Fixed
- fix major date problem where year would render with incorrect number of days for different timezones [issue#4](https://github.com/Richardsl/heatmap-calendar-obsidian/issues/4).
- fix problem with certain entries not showing up in the correct month
- fix grid cells not scaling correctly with browser width, especially content in grid cells


#### [0.1.0] - 2022-02-23
- initial release
