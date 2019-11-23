# tiki PostGreSQL
Crawl categories and products from tiki.vn, store in PostGres database and use Flask to query the data. Plus an analysis presentation embed in the Flask website.
Heroku app and database is deployed at [https://tiki-postgresql-app.herokuapp.com](https://tiki-postgresql-app.herokuapp.com)

## Crawling data
1. Prepare the database
    - Here we use python psycopg2 module to create connection to postgres
    - run `Tiki_crawl_categories.ipynb` to crawl categories on Tiki and store in categories table
    - run `classProduct.ipynb` to crawl products on Tiki and store in products table
    - Optionally we use multithreading with Python `concurrent.futures ThreadPoolExecutor` to speed up crawl time. When using multhreading, new connection to db has to be created and closed each access to the db because the connection cannot handle multiprocessing.
    - Product data include: product title, brand name, regular price, discount, final price, category, comments, number of ratings, TikiNOW availability, image, link
    - Database schema:
    ![](https://i.imgur.com/3hurCl3.png)
2. Data analysis
    - Our analysis is in `Analysis.ipynb` 
    - We perform category analysis, seller analysis and product analysis. We use `seaborn` to make charts.
  
## Build Flask app
1. Database connection
    - We use `flask_sqlalchemy SQLAlchemy` to create data class models and query 
    - Database can be query in realtime by input URL path
2. Start Flask app
    - On terminal run  `python app.py` to start the Flask app

## Using the Flask app
1. Navigate using the URL
    - The URL path is used to input query to the app
    - /product/getid/[id] will query and return the product by id
    - /product/getseller/[seller] will query by seller and return all products from the seller ([seller] input is case sensitive), for example, /product/getseller/FORD will return all products by FORD
    - /category/getid/[id] will return the category by id
2. View Tiki analysis presentation
    - Tiki Analysis is embed at /presentation and can be viewed by going to /presentation or clicking on 'Go to Tiki analysis Slides' button

## Push and deploy to Heroku
1. Create app and push database to Heroku
    - Install heroku and login
    - Create environment `.env` and install required python modules. (Flask, flask_script, flask_migrate, psycopg2-binary, gunicorn)
    - Create `requirements.txt` by `pip freeze > requirements.txt`
    - Create app `heroku create [app-name]`
    - Create remote and ready to push `git remote add prod https://git.heroku.com/tiki-postgresql-app.git`
    - Config heroku `heroku config:set APP_SETTINGS=config.ProductionConfig --remote prod`
    - Create database remotely `heroku addons:create heroku-postgresql:hobby-dev --app [app-name]`
    - View configurations by `heroku config --app [app-name]`
    - push postgres db to remote `PGUSER=postgres PGPASSWORD=password heroku pg:push postgresql://postgres:password@localhost:5432/thuctamdb DATABASE_URL --app [app-name]` . The `DATABASE_URL` is the in heroku configurations in the previous remote db creation step
    - If `pg_dump` version mismatch upgrade postgres by `brew upgrade postgresql`
    - run `git init` on the root folder that has not been git initialized
    - `git commit -m "message here"` then `git push prod master`
    - All done and go to heroku url to use app

