Sometimes, we need to display numerical outputs that depend on certain criteria. For example, users may want to show a specific text or value based on the outcome of a measure or calculation. The difficulty is to be able to do this for different visualizations. For example, some visualizations may be more suitable for accepting text outputs than others. Sometimes we may need to use different methods to get the expected result. In this section there are different scenarios to how to manage output customizations using DAX measures.

## Custom number formatting
In many cases, we can change the output of a calculation depending on the outcome of another calculation. For example, we can instruct Power BI to show a text if another calculation matches a certain result or a set of results. This section outlines how to adjust the apparent format of a calculation based on some conditions. There’s two main steps to achieve the custom formatting of a calculation:
1. Gather the calculation of the total that will be evaluated for the given visualization and find the absolute value to use later on the range evaluation
2. Evaluate the range and specify the output as follows:
* If total is blank or zero then “N/A”
* If total is between 0 and 1 then “<”
* If total is between 1 and 1,000 then format number as $1,234 if positive or ($1,234) if negative
* If total is between 1,000 and 1,000,000 then format number as $1.2K if positive or ($1.2K) if negative
* If total is greater than 1,000,000 then format number as $1.2M if positive or ($1.2M) if negative

![QT](https://github.com/anusiner92/powerbicustomformat/assets/54285571/e10415b9-4208-45f7-8f57-e5b2ce371fa2)

For this case, the outputs are all in text form. These outputs work for visualizations that take in and show out text. These visualizations are card(s), table, matrix, and textbox. For visualizations that need an axis with numbers, there are more steps to follow. The next section explains how to get a custom format using bar charts or other visualizations that need an axis.

## Bar chart customization

To display text values for the sum of each column on a column chart, we need to follow some extra steps. This section shows the steps and requirements for showing conditional text output above a column in a column chart visualization in Power BI. This solution uses the Line and stacked column chart in Power BI to show the bars and use the line as the sum of the column so that we can control the output of that specific series.

The first step is to create the format measure explained in section 2.1. This measure will determine the output of the sum and will be the presentation part of the solution. The second step is to create a position measure. Since we will use the line and stacked column chart, we need to control the position (or size) of the line so that it always shows above the columns. The challenge arises when there is a part of the column chart that is negative. The image below shows this scenario. In this case we still want to show 80 as the sum of the second column but keep its position above the column. For this reason we will create a measure just for the position of the sum that will change all sums to positive. For this implementation it is important to note that it will only work if all the legend values are known in advance. Otherwise we can’t replace specific negatives to a zero value.

![image](https://github.com/anusiner92/powerbicustomformat/assets/54285571/b0d80a4b-b5ed-4db9-a074-a562c46c3ecb)

This measure only determines the position and not the value that is displayed. With this method, the position of the second column is 90, with an extra 5% buffer for padding, even if the total is 80. The value that appears in the second column will be 80 or the conditional value that the format measure sets for the visualization.

Here is a sample of the position measure when we want to display quarterly sums for a legend with 3 different values (Audio, AV, and International). In this measure we will add up the amounts for each category separately and then show 0 for this sum if the calculation is anything less than zero. In the end we tie it all together by summing all three totals and multiplying by 1.05 to add padding to the position of the total.

![Position](https://github.com/anusiner92/powerbicustomformat/assets/54285571/56835dc8-5061-472b-b4b5-857f77e9f8bc)

The following steps describe how to put trending visualizations together

1. Add a line and stacked column chart

![image](https://github.com/anusiner92/powerbicustomformat/assets/54285571/d5dab537-4cc5-4130-aead-844abd705178)

2. Add the trending column to the X-axis of the visualization (e.g. Dist Period, Quarter, etc.)
3. Add the column total to the column Y-axis of the visualization. (e.g. Quarterly Trends Total Earnings, Total Pay Amount, etc.)
4. Add the appropriate column to the legend section in the visualization
5. Add the position measure to the line Y-axis

At this point, the visualization may look something like this. Where all three legends are present plus the line that we added as the position.

![Chart](https://github.com/anusiner92/powerbicustomformat/assets/54285571/ec19d3f8-88df-479d-9988-792d393e309a)

6. In the semantic model, find the measure that defines the format. This is the measure that shows the total output. Make its data format numeric. This is because the Format measure of this visual will go on the Y-axis of the line and this field in the visual only takes numeric data types. Because the measure gives text, the file won’t let you change to any numeric format. So, a way to do this is to comment out the part after the RETURN statement and put a zero (0) instead. The measure should then switch to a whole number format automatically.

![Measure code](https://github.com/anusiner92/powerbicustomformat/assets/54285571/a337b4bb-60c7-4fbd-9bbd-cde516f57fac)

7. Now, save and publish the file to update the current semantic model and let the visualization file use this format measure with the numeric formatting.
8. Once published and replaced, refresh the visualization file to grab the latest refresh into the workspace.
9. Grab the Format measure and add it to the line Y-axis of the visualization

At this point, the visualization may look as shown below, with both position and format measures on the line Y-axis (note 0 line at the bottom of the Y-axis):

![Chart2](https://github.com/anusiner92/powerbicustomformat/assets/54285571/ab0e1f2d-03a1-44c5-bd4b-62f9e13e96a6)

10. Return to the semantic model file and uncomment the formatting conditions on the output of the format measure and republish to replace the past version of the semantic model.

![QT](https://github.com/anusiner92/powerbicustomformat/assets/54285571/e42f0ba3-527a-46ad-9f7f-63c2afc19b37)

11. Refresh the visualization file to grab the latest publish on the target workspace. At this point the format line will go away from the visualization but both measures will remain in the field value

![Chart 3](https://github.com/anusiner92/powerbicustomformat/assets/54285571/e85973b0-e471-43c2-8ae2-95b8dd0c55f1)

12. Access the format of the visualizations and turn on the data labels menu. 

![image](https://github.com/anusiner92/powerbicustomformat/assets/54285571/ffb2067e-a7da-40fc-aa00-fb26ccf84cce)

13. Expand the data label menu to edit specific formatting for each piece of the column as well as line. Expand Series and select each legend option and turn off the option for “Show for this series”. Keep the position measure turned on.

![image](https://github.com/anusiner92/powerbicustomformat/assets/54285571/e7b9f28b-5295-41de-9eab-c3f5048e2519)
           
14. Select the position measure in the series dropdown and expand the Value sub menu in the data labels format menu. Replace the Field value with the specific Format measure that’s applicable to the visualization that’s being worked on currently.
        
![image](https://github.com/anusiner92/powerbicustomformat/assets/54285571/b599b3bb-868f-4f8f-84ad-42f1e455690b)

15. Expand the Options sub menu under the Data labels menu and set Position (line) = Above 

![image](https://github.com/anusiner92/powerbicustomformat/assets/54285571/2517d5ab-51d4-4e1f-97c9-686f7e698fe9)

16. Expand the lines menu and set the width to 0px this way we can still use the line but it will be invisible since we only want to use it for its data label 

![image](https://github.com/anusiner92/powerbicustomformat/assets/54285571/71c90b46-ed72-4fd7-a658-4f9ffd92014d)

## Visualization groupings
To make a legend for these custom bar charts that leaves out the total (line), we need to make a secondary visualization that will serve as the legend. The secondary visualization will go on the layer under our main visualizations and will be grouped to keep order in all the visualizations that we will make on the page. These groups are useful for both getting the look and the functionality that we want, that might not be possible with one visualization alone. To see how the visualizations are grouped, look at the View pane in the visualization .pbix file. All the groups are shown with expand menus. Each expand menu has a new name to make it easier to tell what it is. For example, on the Overview page, the Quarterly Earnings Trends visual is made from 4 different separate visualizations:

![image](https://github.com/anusiner92/powerbicustomformat/assets/54285571/f9b14d77-ceec-48f4-98ab-3493b76f9d95)

The order of layers is also intentional for each visualization group. These layers help to achieve the intended effect for each group. For instance, the legend visualization on a trending chart will always be placed at the lower part of the group to make it look like it belongs to the visualization that displays the actual trending data. Similarly, the cover shapes/visuals will go over other visualizations to prevent users from interacting with certain elements and to show conditional messages.

![image](https://github.com/anusiner92/powerbicustomformat/assets/54285571/c6cd63fe-fef4-4282-bf60-77a7945f00a9)
