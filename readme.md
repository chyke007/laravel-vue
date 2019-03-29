# Full Stack Application built with Laravel and VueJs

Laravel 5.5 API that uses the API resources with a Vue.js frontend

## Getting Started
* Clone or download this repo
* Run composer install
* Edit .env to you database credentials
* Run migration
* Run seed
* Run compile for JS
* Serve the application

### Prerequisites

Install the following 

```
Composer
```

### Installing

```
# Install Dependencies
composer install

# Run Migrations
php artisan migrate

# Import Articles
php artisan db:seed

# If you get an error about an encryption key
php artisan key:generate

# Install JS Dependencies
npm install

# Watch Files
npm run watch

```

## Endpoints
### List all articles with links and meta
```
GET api/articles

```
### Get single article
```
GET api/article/{id}

```
### Delete article
```
DELETE api/article/{id}

```
### Add article
```
POST api/article
title/body

```
### Update article
```
PUT api/article
article_id/title/body

```
## Built With

* [Laravel](https://laravel.com/) - The web framework used
* [VueJs](https://vuejs.org/) - Used for front end functionality
* [Bootstrap](https://getbootstrap.com/) - Used for the UI



## Versioning

We use [SemVer](http://semver.org/) for versioning. Current version is 1.0.0

## Authors

* **Nwachukwu Chibuike** - *Initial work* - [NodeRest](https://github.com/chyke007/hackvotes)

## License

This project is licensed under the MIT License - see the [LICENSE.md](LICENSE.md) file for details

## Acknowledgments

* Traversy Media
* Shante Austin

