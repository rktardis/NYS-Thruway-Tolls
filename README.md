# 789-working-final# -*- coding: utf-8 -*-"""Created on Sun May  10 14:23:45 2023@author: Richard Kaufman IV"""   (1)  concisely explain the purpose of the analysis;   (2) include instructions on how to obtain the original input data, such as where it can be downloaded or it should provide a script to download the data via an API;   (3) explain what each script does and the order in which they should be run;   (4) explain any additional files provided in the repository; and   (5) discuss the results. # New York Thruway Tolls, Usage, and You!## IntroductionThis project aims to analyse publicly available data on usage of the Thruway, a highway system in The State of New York connecting most major cities accross the state from New York City to Albany, Syracuse, Buffalo, and points between.Built in stages, the first section of the Thruway opened in 1954, with the entire main line from New York City to Pennsylvania being finished in 1957. ## Data### New York State provided data downloadsData for this analysis is provided by the state of New York via <data.ny.gov>. The Thruway database can be found [here](https://data.ny.gov/browse/select_dataset?Dataset-Information_Agency=Thruway+Authority "NYS Thruway Data").The database provides data in two timeframes:     *Broken down by the hour, provided in year chunks[^1]    *Broken down by the quarter hour, provided in quarter chunks[^2]    Six downloads from this site are required:1. `NYS_Thruway_Origin_and_Destination_Points_for_All_Vehicles_-_1_Hour_Intervals__2015`2. `NYS_Thruway_Origin_and_Destination_Points_for_All_Vehicles_-_1_Hour_Intervals__2016`3. `NYS_Thruway_Origin_and_Destination_Points_for_All_Vehicles_-_1_Hour_Intervals__2017`4. `NYS_Thruway_Origin_and_Destination_Points_for_All_Vehicles_-_1_Hour_Intervals__2018`5. `NYS_Thruway_Origin_and_Destination_Points_for_All_Vehicles_-_1_Hour_Intervals__2019`6. `NYS_Thruway_Origin_and_Destination_Points_for_All_Vehicles_-_1_Hour_Intervals__2020`The data, in addition to the date and time, provides the height and number of axles of the vehicle, the exit at which the vehicle entered the Thruway, the exit at which the vehicle exited the Thruway, whether the vehicle payed its toll by the "EZ-Pass" cashless tolling system or cash, and the number of vehicles that made that trip during that time.Below is an example of the data, showing that 23 2 axle vehicles under 7 feet 6 inches in height went from exit 27 to 26 using EZ-Pass during the 11pm hour of March 23rd, 2001.| Date | Entrance | Exit | Interval Beginning Time | Vehicle Class | Vehicle Count | Payment Type || --- | ---: | ---: | ---: |--- | ---: | --- || 03/23/2001 | 27 | 26 | 23 | 2L | 23 | EZ-Pass |### Generated input dataThere are 2 .csv files included in the repository for use in the scripts:1. "class_references.csv"  - The data provided by the state categorizes vehicles by number of axles (2, 3, 4, 5, 6, or 7+), and height below (L) or above (H) 7 feet 6 inches  - This is not entirely helpful for the analysis, so this file categorizes each class as either passenger (2 axles below 7 feet 6 inches) and Commercial (all others).2. "NYS_Thruway_Mile_Markers.csv"  - This file contains the milemarkers for each exit[from the New York State Thruway Authority](https://www.thruway.ny.gov/travelers/interchanges/index.html "Thruway Milemarkers")  - Also contained in this file are "reference" exit numbers, in numerical order starting at 101. The purpose of these numbers is to provide an integer for the scripts to work with to differentiate exits during analysis. Though the exits are already "numbered," they are not all necessarily integers, as there are many "A" exits along the thruway, along with several "B" exits, and one "H" exit.    - "A" exits are infill exits, where an exit was constructed between two existing exits. Not wanting to number all the exits north and west of this new exit, the new exit was assigned the same number as the nearest exit to the south/west, along with an "A"[^4]    - "B" exits are exits on the Berkshire Connector, a portion of the Thruway that branches off from the main line at exit 21A and runs east to the Massachusetts border, where it becomes the Massachusetts Turnpike (the "Mass Pike"). The connector serves as a bypass for those traveling from Massachusetts to the Thruway, avoiding the often congested, untolled section of Interstate 90 ("Free 90") that runs through downtown Albany. "B" exits are numbered sequencially, with a "B" prefix.[^5]    - The "H" exit is the Harriman Toll Gantry. It is located between exits 16 & 17, and is numbered "16H"    ## ScriptsThere are X scripts provided in the repository. Two are used to compile and format the data, and X use that built data to conduct the analysis. The data building scripts should be run first in the numbered order below, and for the analysis scripts, Pair_Data is a prerequisite to Segment_Usage.#### Data Building Scripts1. Data_Compiler.py  - This script uses the six individual data files downloaded from the state and performs several actions:    - Splits the month, day, and year into three distinct columns    - Groups it by month, removing the hour and date    - Sets the type of certain columns    - Appends year data together into one file2. Data_Builder.py  - Data_Builder uses the milemarker data, class references, and data file generated in Data_Compiler and performs several functions:    - Merges milemarker data to add the exit reference numbers    - Replaces exits "B1," "B2," and "B3" with exit "21A," and "16H" with "16"    - Removes data from west of Exit "50"[^6]    - Simplifies class data to "Passenger" or "Commercial"    - Generates a "Pair" column, simplifying data to the different exit pairs, ignoring directionality  - As the data is rather large, this script breaks up the data into eight different sections of the thruway:    - Exits 15 to 18    - Exits 19 to 24    - Exits 25 to 30    - Exits 31 to 36    - Exits 37 to 42    - Exits 43 to 48    - Exits 49 to 54    - Exits beyond 55 and the Berkshire Connector    #### Analysis Scripts1. Pair_Data.py  - This script generates "Thruway_Pair_Data.csv," a file that contains the total count of all trips during the study period, Q1 2015 to Q3 2020, between each pair of exits.  - To create this data, the data saved from Data_Builder was used.2. Segment_Usage.py  - This script generates "segment_data.csv," a file that contains the usage data for each individual segment of the Thruway.[^7]  - To create this data, the data saved from Pair_Data was used.  - This script also generates a histogram, "Segment_Usage.png," visualizing the usage accross the system.3. Usage_Over_Time.py  - This script generates "rel_use_time_class.csv," a file that contains the usage data over time for each individual segment of the Thruway.  - To create this data, the data saved from Data_Builder was used.  - This script also generates a dual line graph, "Segment_Usage.png," visualizing relative usage of the system over time by both passenger and commercial vehicles."Relative_Usage_Over_Time_by_Type.png"## Analysis#### Pair UsageThe file "Thruway_Pair_Data.csv" contains the number of times in the study timeframe every possible journey on the Thruway was made. This contains some very interesting findings, though not entirely surprising, especially to those who have lived in the Captial Region:1. The most popular trips on the Thruway are likely driven by commuters.  - The number #1 most popular trip was between exits 24 and 25    - This trip was made  52,700,443 times during the study period, nearly double the next     - This is likely due to the high number of commuters who work in Albany (exit 24) and live along Interstate 890 (exit 25).    - Exit 24 to 25A (5th) and 23 to 25 (8th) are likely also popular commuter routes  - Other top performers are also commuter routes:    - Exits 49 & 50 in Buffalo (2nd)    - Exits 15 & 17 downstate (3rd)    - Exits 44 & 45 in Rochester2. Shorter trips are more popular  - Only two trips in the 20 most popular were longer than 5 exits[^8]  - Seventeen of the 25 most popular trips were 1, 2, or 3 exits long  - Only 15 of the top 100 trips were 8 exits or longer#### Segment UsageThe file "Segment_Usage.png" shows how much each individual segment between sequential exits is used.  ### Notes[^1]: i.e. all the trips for the year 2020, broken down by the hour in which they occurred.[^2]: i.e. all the trips for January, February, and March broken down by the fifteen [15] minute block in which they ocurred.[^3]: These milemarkers are relative to where numbering begins, at the northern terminus of the Major Deegan Expressway in the Bronx, not where tolling begins near Exit 15 at the end of its concurrence with Interstate 287 and begining of concurrence with New York State Rt. 17.[^4]: i.e. Exit "25A" was opened in 1985, well after the completion of the larger Thruway, to connect the newly opened Interstate 88 to the Thruway. As it is between exits "25" and "26", it was assigned the number 25 with an "A."[^5]: The one exception is exit "21B"; as there are two infill stations between exit 21 and 22.[^6]: West of exit 50, not all sections are tolled, so data is spotty.[^7]: i.e. if a vehicle traveled from exit 27 to 24, that vehicle would be counted in the usage for 24 to 25, 25 to 25A, 25A to 26, and 26 to 27.[^8]: These trips were exit 15 to exits 23 (14th) & 24 (6th).