# web_scraping_challenge
## Part 1: Scrape Titles and Preview Text from Mars News (40 points)
1. Automated browsing (with Splinter) was used to visit the Mars news site, and the HTML code was extracted (with Beautiful Soup). (10 points)
    - Used the following code to accomplish this task. 

            # Visit the Mars news site
            url = 'https://static.bc-edx.com/data/web/mars_news/index.html'
            browser.visit(url)

    - **Code for this was sourced from activities performed in class**

2. The titles and preview text of the news articles were scraped and extracted. (20 points)
    - First scraped the section I wanted to pull the code from

            # Extract all the text elements
            articles = soup.find_all('div', class_='list_text')
            articles

    - Then I created an empty list to store the dictionary of titles and preview text

            # Create an empty list to store the dictionaries
            dict_list = []

    - I created a for loop through the text elements making sure to extract the title and preview text for each article
    - I stored the title/preview text in a dictionary I created
    - Then I add the dictionary to my list

            # Loop through the text elements
            for article in articles: 
                # Extract the title and preview text from the elements
                title = article.find('div', class_='content_title').text.strip()
                preview = article.find('div', class_='article_teaser_body').text.strip()
                # Store each title and preview pair in a dictionary
                dictionary = {
                    'title': title,
                    'preview': preview
                }
                # Add the dictionary to the list
                dict_list.append(dictionary)

    - **Code for this was taken from activities performed in class**

3. The scraped information was stored in the specified Python data structure—specifically, a list of dictionaries. (10 points)
    - I stored the title/preview text in a dictionary I created
    - Then I add the dictionary to my list
    - This was within my for loop above in the code

            # Store each title and preview pair in a dictionary
                dictionary = {
                    'title': title,
                    'preview': preview
                }
                # Add the dictionary to the list
                dict_list.append(dictionary)

    - **Code was taken from activities performed in class**

## Part 2: Scrape and Analyze Mars Weather Data (60 points)
4. The HTML table was extracted into a Pandas DataFrame. Either Pandas or Splinter and Beautiful Soup were used to scrape the data. The columns have the correct headings and data types. (15 points)
    - First connected to the site

            url = "https://static.bc-edx.com/data/web/mars_facts/temperature.html"
            browser.visit(url)

    - created a beautiful soup object 

            # Create a Beautiful Soup Object
            html = browser.html
            soup = soup(html, 'html.parser')

    - Extracted the data and put it into a pandas DataFrame

            # Extract all rows of data
            table = soup.find('table', class_='table')

            #Further filtered the results to only show me the data not including the headers
            row_data = table.find_all('tr', class_='data-row')

            # Create an empty list
            table_list = []
            # Loop through the scraped data to create a list of rows
            for row in row_data:
                #Need to find all the td data within the 'data-row' class
                cells = row.find_all('td')
                #Create a dictionary to store the table data
                table_data = {
                    'id': cells[0].text.strip(), 
                    'terrestrial_date': cells[1].text.strip(), 
                    'sol': cells[2].text.strip(), 
                    'ls': cells[3].text.strip(), 
                    'month': cells[4].text.strip(), 
                    'min_temp': cells[5].text.strip(), 
                    'pressure': cells[6].text.strip()
                }
                #Add the dictionary to the list
                table_list.append(table_data)

            # Create a Pandas DataFrame by using the list of rows and a list of the column names
            mars_table = pd.DataFrame(table_list)

    **Code for this was mostly taken from activities in class. I had some trouble with the for loop, specifically the creating a dictionary to store the table data part. ChatGPT helped me by putting the text.strip in each value I was creating. I had originally tried using it when I finding all cells.**

5. The data was analyzed to answer the following questions: (10 points)
    - How many months exist on Mars? (5 points)
        - I was able to find that 12 months exist on Mars. 

                # Create a Pandas DataFrame by using the list of rows and a list of the column names
                mars_table = pd.DataFrame(table_list)

    - How many Martian days' worth of data are there? (5 points)
        - I found that there are 1867 days of mars data

                # 2. How many Martian days' worth of data are there?
                days_data = mars_table['sol'].nunique()

    - **Code for this was taken from previous activities we had performed in class working with pandas.**

6. The data was analyzed to answer the following questions, and a data visualization was created to support each answer: (30 points)
    - Which month, on average, has the lowest temperature? The highest? (10 points)
        - to calculate this I grouped by the month and then calculated the average min temp per month
        - I then visualized the results using a bar chart
        - **I was able to determine that month 3 has the lowest average temperature**

                # 3. What is the average low temperature by month?
                low_temp_per_month = mars_table.groupby('month')['min_temp'].mean()

                # Plot the average temperature by month
                # use matplotlib to plot a bar chart from the series
                low_temp_per_month.plot(kind='bar')
                plt.xlabel("month")
                plt.ylabel("Temperature in Celsius")
                plt.show()

        - I used my same grouping from earlier, but sorted the results in ascending order
        - I then visualized the results on a bar chart 
        - **I was able to determine that month 8 had the highest average temperature**

                # Identify the coldest and hottest months in Curiosity's location
                # first sort the data in ascending order to math the example
                low_temp_per_month_asc = low_temp_per_month.sort_values(ascending=1)
                # use matplotlib to plot the results on a bar chart
                low_temp_per_month_asc.plot(kind='bar')
                plt.xlabel('month')
                plt.ylabel('Temperature in Celsius')
                plt.show()

        - **Code for this was taken from previous activities we had performed in class using matplotlib to visualize data**

    - Which month, on average, has the lowest atmospheric pressure? The highest? (10 points)
        - I was once again grouped by month and then calculated average pressure per month
        - I then visualized the results on a bar chart 
        - **Able to determine that month 6 had the lowest pressure and month 9 had the highest pressure**

                # Plot the average pressure by month
                # to match the graph in the starter code we sort in ascending order
                avg_pressure_month_sort = avg_pressure_month.sort_values(ascending=1)
                # use matplotlib to plot the results on a bar chart
                avg_pressure_month_sort.plot(kind='bar')
                plt.xlabel('month')
                plt.ylabel('Atmospheric Pressure')
                plt.show()

        - **Code for this was taken from activities we performed in class**

    - How many terrestrial days exist in a Martian year? A visual estimate within 25% was made. (10 points)
        - To get this number I plotted the terrestrial days vs temperature on a chart and then eyeballed the differences between the peaks of the data. 
        - **I looked at the lowest temps as they were easiest for me to guestimate. The right-most low peak is right around 1750 and the next one over is around 1100. So 1750-1100=650 days. This is fairly close to the actual answer from the internet of 687 days**

                # 5. How many terrestrial (earth) days are there in a Martian year?
                # Visually estimate the result by plotting the daily minimum temperature
                # created a new column to count the number of days for each date
                mars_table['num_date'] = range(1, len(mars_table) + 1)
                # plot the results on a graph
                plt.plot(mars_table['num_date'], mars_table['min_temp'])
                plt.xlabel('Number of terrestrial day')
                plt.ylabel('Minimum temperature')
                plt.show()

        - **ChatGPT helped me create a new column to calculate the number of days for each earth date listed. I then plotted the results of the number of days vs temp using activities found in class**

8. The DataFrame was exported into a CSV file. (5 points)
    - Used .to_csv to convert the dataframe to a CSV file

            # Write the data to a CSV
            mars_table.to_csv("Output/mars_dataframe.csv", index=False, header=True)


