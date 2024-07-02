# Netflix data analysis
 analysing the data of Netflix movies/Tv shows

![alt](Netflix-Projects.gif)



### <span style="color: red;">Problem Statement </span>
- The primary objective of this project is to extract valuable insights from the available Netflix dataset. These insights should aid Netflix in making informed decisions regarding the types of movies and TV shows to produce and strategize business growth in different countries.

### initial findings

- it is intersting to note that there are as many as 1793 Tv shows that has only 1 season
- the frequency in rating suggests that TV-MA is the most rated type , it basically means Mature Audience Only
- No. of movies produced in United states (2845) are higher than other countries 
- threre seems to be a lot of Missing Director names here, the reason can be that a lot of Tv shows has different directors directig perticular episode and naming all wouldnt be possible here!(for example a Tv show called 'tales of the city' has 10 episodes and 6 directors to direct them!)
- Rajiv Chilaka is most likely a director thas has most movies/Tv shows directed in the dataset but we will confirm that in our further analysis 
- 2018 seems to be the year when most of movies/tv shows were, released, we will precisely check this in our futher analysis 
- 1st Jan 2020 seems to be the date when highet number of movies/tv shows were added, we will verify this as well in our analysis further 
- Dramas, International Movies are the most opted genere, we will narrow down further to find the top number.
- further diving into data it is found that there are movies with dubbed language as well here, for example Bhoomika , it is available in 4 different languages and there are total of 27 movies/tv shows like that and total number including all languages are 59.
- There are Two ```type``` of Entertainment options as ```Movie``` and ```TV Show```

#### unnesting of columns 

- 'director' column has certain rows where names of two or more director names are present, which clearly indicates that the perticular movie was directed by two or more directors, hence the next steps are defined to expand the dataset in a ```copy``` such that each row consist of only one director, this will enable us to identify the number of movies contributed by each unique director.
- 'country' column has certain rows where names of two or more countries are present, which clealy indicates that the perticular movie was produced in multiple countries, hence the next steps are defined to expand the dataset in a ```copy``` such that each row consist of only one country, this will enable us to identify the number of movies produced by each unique country.
- 'cast' column has certain rows where names of two or more Actor names are present, which clealy indicates that the perticular movie had two or more number of actors, hence the next steps are defined to expand the dataset in a ```copy``` such that each row consist of only one Actor, this will enable us to identify the number of movies each actors worked on.
- 'listed_in' column has certain rows where names of two or more genre are present, which clealy indicates that the perticular movie covers multiple genre, hence the next steps are defined to expand the dataset in a ```copy``` such that each row consist of only one genre, this will enable us to identify the number of movies covered under each genre.
- we could have done all 4 unnesting in a single copy, but this would not be efficient if the dataset was larger in number of rows, hence to make the approach more general for larger datasets we kept unnesting in seperate parts 
- once all the unnesting is done we merge the data using a common unique feature which in our case can be 'show_id' or 'title'

#### Null values handling

- as we can see from the results that out of 11 columns, **6 columns** (>50% of columns) contains null values, out of those 6 columns. 
    - 3 columns are **Director, Cast, rating** [ we will have to fill these with "Unknown" tag as both columns are catagorical and % of nulls is also relatively high]
    - for **'date_added'** column we will impute the values by mode of date_added to the corresponding release_year, for example if we have a null value in date_added column where release year is 2015, we will look for the mode of date_added column when the release year is 2015 and we will fill that value in date_added missing value.
    - **'duration'** ~ for this columns we will take a look at null location to see if there's any simple entry mistakes that can be corrected.
    - **'country'** ~ we will fill these nulls with modes of director and cast country in other titles and the rest of null values will be filled as 'unknown'

        ```def impute_based_on_mode(df, target_col, group_col):
        for group in df[df[target_col].isnull()][group_col].unique():
            if group in df[~df[target_col].isnull()][group_col].unique():
                mode_value = df[df[group_col] == group][target_col].mode().values[0]
                df.loc[df[group_col] == group, target_col] = df.loc[df[group_col] == group, target_col].fillna(mode_value)
        return df

    fdf= impute_based_on_mode(fdf, 'country', 'director')
    fdf= impute_based_on_mode(fdf, 'country', 'cast')```