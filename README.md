## Neesowork application
This application parses resumes or vacancies from [headhunter.ru](https://hh.ru) website and uploads them to the database, allowing to further study and manipulate the collected data.

## Structure
This project is comprised of the **MySQL database**, **python backend** and **html/css/js frontend** run by nginx.

## How to launch
In order to launch the whole project, first clone the repository along with its submodules:
> git clone --recurse-submodules https://github.com/Neesowork/application.git

Then, launch the whole Dockerized system with:
> docker compose up
And navigate to ``localhost`` in your browser

You may launch different parts of the project independently as well, using any *http server* for the frontend, *uvicorn* for backend and *hosting MySQL database* your own way.

## User guide
### Query page
Upon loading the website you are presented with the ``Query`` page.
Here you can query new data to be parsed and loaded to the database.


To do so, press the ``New parse query`` button,
![new_parse_query](https://github.com/Neesowork/application/assets/146841763/00df83b9-48be-4787-b117-d670fa286cf9)

select which type of data to parse (``Vacancies`` or ``Resumes``)
![data_to_parse](https://github.com/Neesowork/application/assets/146841763/31e541cc-8d18-4195-980d-318e125b394e)

and optionally set some search parameters (e.g. ``Search for``, ``Work experience``, ``Schedule`` etc.);
![choose_parse_parameters](https://github.com/Neesowork/application/assets/146841763/56a26a5f-bdf3-4272-b39d-3677e5fc50b9)

press the ``Send`` button to send the request:
![press_send_btn](https://github.com/Neesowork/application/assets/146841763/2c2f85c7-edb3-4e63-958a-b4df14caeaa7)

You can check your request's status at the bottom right of the page:
![request_status](https://github.com/Neesowork/application/assets/146841763/21a6ac68-eb89-49ca-abe7-d4ba4803b964)


While one type of request is being loaded, you may also start querying another one in parallel (i.e. you are able to query ``Vacancies`` while ``Resumes`` are loading and vice versa).
Mind that ``Resumes`` usually take longer to load than ``Vacancies``, and the process may take up to around half a minute, so be patient.
If you see ``Request completed`` notification it means that the data was parsed successfully and will soon be loaded in the database.

Preview of the fetched data can then be viewed for convenience (notice how two types of requests are split to the corresponding tables).
![lookup_fetched_data](https://github.com/Neesowork/application/assets/146841763/55b50d8b-8ad4-447c-b231-ff8d305fad99)


When you are ready to work with the database, navigate to its page by pressing the corresponding label next to "Neesowork" text at the top of the page:
![to_database_page](https://github.com/Neesowork/application/assets/146841763/5241ed3e-cc8d-4893-ba19-97a45ddaa49f)

### Database page
Here you can view the database and filter what's being displayed.
(You can always returns to the ``Query`` page by pressing the label next to "Neesowork" text, although parse result previews / database filters do not save between the pages)


By default, this page displays the whole database contents for ``Resumes`` and ``Vacancies`` (use tabs to choose which table to display).
You can scroll down indefinetily to load more and more rows of the tables until they are fully shown.


When you are ready to apply some filters to the data, press the corresponding icon at the top of the page to open ``Filters`` menu:
![to_manage_filters](https://github.com/Neesowork/application/assets/146841763/984c9932-2f80-4910-80c1-9fd08eda8257)

*Filters* are a quite flexible tool to filter database data to your liking. 
![filters_menu](https://github.com/Neesowork/application/assets/146841763/0c8d6246-0d89-4188-a0d1-47c490e84ff7)

You can add a filter to ``Vacancies`` and/or ``Resumes`` table/s by pressing ``New filter`` button.
![filter](https://github.com/Neesowork/application/assets/146841763/ab5c98a7-7317-4a4d-b40e-d686942a00ae)

Each filter consists of a required ``Filter for key`` field (which column it should be applied to),


``By string`` field (what should a value in that column match with in order for the corresponding row to be displayed),


``Order by`` switch (should the data be ordered),


``Order`` select field (if data is being ordered, in which direction),


and ``Remove filter`` button (removes the filter above itself).


Filters are stacked on top of each other and execute internally from top to the bottom as well;
you can imagine the word 'AND' between each filter with different keys and 'OR' between the 
filters of the same key.


In ``By string`` field there are special characters accepted: ``%`` for any amount (or none) characters, and ``_`` for an unkwown single character.
So, if you wanted to filter all names containing the word 'Java', you would write '%Java%' in this field for the ``Name`` key ('Java' would find only names that are *exactly* 'Java').


If several field are being ordered, the priority is from top to the bottom.


For instance, the following two filters can be read as:
![filter_example](https://github.com/Neesowork/application/assets/146841763/bf18e642-ccb3-4fc7-a0a4-ff15afa7c6f0)
> Find all rows where area starts with an 'А' (and put them in an alphabetical order) and then sort the salaries in descending order (not breaking the alphabetical sorting)

Numerial filtering is not supported here.


When you've set all the filters you wanted, press ``Apply``:
![filters_apply](https://github.com/Neesowork/application/assets/146841763/94560a9b-2e02-468a-883c-4b97b78ce802)

The data displayed in the tabs ``Resumes`` and ``Vacancies`` is now filtered.

## Troubleshooting
The backend should start after the MySQL database starts to accept connections. To provide this order this project has a custom healthcheck that pings the database:
```
            test: ["CMD", "mysqladmin" ,"ping", "-h", "localhost"]
            timeout: 20s
            retries: 10
```
Increase ``timeout / retries`` if it seems like backend starts before the db is ready.
