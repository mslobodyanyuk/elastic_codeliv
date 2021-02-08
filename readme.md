Elastic Search integration in laravel application
=================================================

* ***Actions on the deployment of the project:***

	- Making a new project elastic_codeliv.loc:
																			
	sudo chmod -R 777 /var/www/LARAVEL/Elasticsearch/elastic_codeliv.loc

	//!!!! .conf
	sudo cp /etc/apache2/sites-available/test.loc.conf /etc/apache2/sites-available/elastic_codeliv.loc.conf
			
	sudo nano /etc/apache2/sites-available/elastic_codeliv.loc.conf

	sudo a2ensite elastic_codeliv.loc.conf

	sudo systemctl restart apache2

	sudo nano /etc/hosts

	cd /var/www/LARAVEL/Elasticsearch/elastic_codeliv.loc

- Deploy project:

	`git clone << >>`
	
	_+ Сut the contents of the folder up one level and delete the empty one._

	`composer install`		

---

Code Liv

[Elastic Search integration in laravel application (16:50)]( https://www.youtube.com/watch?v=aCpsSIY_2eU&ab_channel=CodeLiv )

[(1:30)]( https://youtu.be/aCpsSIY_2eU?t=90 )

	composer create-project laravel/laravel elastic_codeliv.loc "5.7.*"

Error: 

<https://www.nicesnippets.com/blog/proc-open-fork-failed-cannot-allocate-memory-laravel-ubuntu>

	free -m
	sudo /bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
	sudo /sbin/mkswap /var/swap.1
	sudo /sbin/swapon /var/swap.1

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/1.png )

`composer.json`:

```
"elasticquent/elasticquent": "dev-master"
```

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/2.png )

[(3:25)]( https://youtu.be/aCpsSIY_2eU?t=205 )

	composer update

Error:

<https://www.nicesnippets.com/blog/proc-open-fork-failed-cannot-allocate-memory-laravel-ubuntu>

	free -m
	sudo /bin/dd if=/dev/zero of=/var/swap.1 bs=1M count=1024
	sudo /sbin/mkswap /var/swap.1
	sudo /sbin/swapon /var/swap.1


[(4:00)]( https://youtu.be/aCpsSIY_2eU?t=240 )

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/3.png )

`config/app.php`:

```php
'providers' => [
...
Elasticquent\ElasticquentServiceProvider::class,
],
...

'aliases' => [ 
...
'Es' => Elasticquent\ElasticquentElasticsearchFacade::class,
],
```

[(4:40)]( https://youtu.be/aCpsSIY_2eU?t=280 )

	php artisan vendor:publish --provider="Elasticquent\ElasticquentServiceProvider"

[(5:30)]( https://youtu.be/aCpsSIY_2eU?t=330 )

`config/elasticquent.php`:

```php
'default_index' => 'articles',
```

[(5:45)]( https://youtu.be/aCpsSIY_2eU?t=345 )

	php artisan make:model Article -m

[(6:40)]( https://youtu.be/aCpsSIY_2eU?t=400 )

`...create_articles_teble.php` migration code in `up()` function:

```php
Schema::create('articles', function (Blueprint $table)){
	$table->increments('id');
	$table->string('title');
	$table->text('body');
	$table->string('tags');
	$table->timestamps();
});
```

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/4.png )

[(7:30)]( https://youtu.be/aCpsSIY_2eU?t=450 )

Create a database in phpMyAdmin:

```
elastic_codeliv - utf8mb4_general_ci
```

[(7:50)]( https://youtu.be/aCpsSIY_2eU?t=470 )

`.env`:

```
DB_DATABASE=elastic_codeliv
DB_USERNAME=your_username
DB_PASSWORD=your_password
```

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/5.png )

[(8:15)]( https://youtu.be/aCpsSIY_2eU?t=495 )

	php artisan migrate


[(8:20)]( https://youtu.be/aCpsSIY_2eU?t=500 ) ERROR:

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/6.png )

[(8:46)]( https://youtu.be/aCpsSIY_2eU?t=526 )

`AppServiceProvider.php` in `boot()` function:

```php
Schema::defaultStringLength(191);
```	

[(9:00)]( https://youtu.be/aCpsSIY_2eU?t=540 )

delete created tables in `phpMyAdmin`:

[(9:20)]( https://youtu.be/aCpsSIY_2eU?t=560 )

	php artisan migrate

	php artisan config:cache
	
	php artisan cache:clear

[(9:30)]( https://youtu.be/aCpsSIY_2eU?t=570 )

	php artisan make:seeder ArticleTableSeeder


[(9:50)]( https://youtu.be/aCpsSIY_2eU?t=590 )

`Article.php`:

```php
protected $fillable = ['title', 'body', 'tags'];
```

[(10:40)]( https://youtu.be/aCpsSIY_2eU?t=640 )

Create table rows in `ArticleTableSeeder` in `run()` function through cycle with using `Fake Factory`:

```php
use App\Article;
...
$faker = Faker\Factory::create();

for($i=0; $i<50; $i++) {
	Article::create([
		'title' => $faker->sentence(3),
		'body' => $faker->paragraph(6),
		'tags' => join(',', $faker->words(4))		
	]);
}
```

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/7.png )

[(11:00)]( https://youtu.be/aCpsSIY_2eU?t=660 )

`DatabaseSeeder` in `run()` function:

```php
$this->call(ArticleTableSeeder::class);
```

[(11:15)]( https://youtu.be/aCpsSIY_2eU?t=675 )

	php artisan db:seed

[(11:25)]( https://youtu.be/aCpsSIY_2eU?t=685 )

_"Set Up Elastic in Article model..."_

```php 
use Elasticquent\ElasticquentTrait;
...
{
	use ElasticquentTrait;
	protected $fillable = ['title', 'body', 'tags'];
	protected $mappingProperties = array(
		'title' => [
			'type' => 'text',
			"analyzer" => "standard",			
		],
		'body' => [
			'type' => 'text',
			"analyzer" => "standard",			
		],
		'tags' => [
			'type' => 'text',
			"analyzer" => "standard",			
		],
	);
}	
```	

[(12:25)]( https://youtu.be/aCpsSIY_2eU?t=745 )

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/8.png )
	
[(13:55)]( https://youtu.be/aCpsSIY_2eU?t=835 )

`routes/web.php`:

```php
use App\Article;

Route::get('/', function () {
	Article::createIndex($shards = null, $replicas = null);
	
	Article::putMapping($ignoreConflicts = true);
	
	Article::addToIndex();
	
	return view('welcome');	
});

Route::get('/search', function () {	
	$articles = Article::searchByQuery(['match' => ['title' => 'Sed']]);
	
	return $articles;
});	
```

[(15:50)]( https://youtu.be/aCpsSIY_2eU?t=950 )

ERROR:

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/9.png )

Solved this by using the command:

	sudo chmod -R 777 /var/www/LARAVEL/Elasticsearch/elastic_codeliv.loc														

`In Browser`:	
	
	http://elastic_codeliv.loc/search

ERROR:

_"No alive nodes found in your cluster"_

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/10.png )
	
`From comments`:

```
Tan Hoang
I have an error. That is: No alive nodes found in your cluster. How can I fix that error?
Arif
it means your elasticsearch service is not up.. should check that
```

[(15:55)]( https://youtu.be/aCpsSIY_2eU?t=955 ) Checking the result: 

`In Terminal`:

	sudo systemctl start elasticsearch.service

`In Browser`:

	http://elastic_codeliv.loc/search

![screenshot of sample]( https://github.com/mslobodyanyuk/elastic_codeliv/blob/master/public/images/11.png )
	
#### Useful links:

Code Liv	

[Elastic Search integration in laravel application]( https://www.youtube.com/watch?v=aCpsSIY_2eU&ab_channel=CodeLiv )
	`+ comments`

<https://www.nicesnippets.com/blog/proc-open-fork-failed-cannot-allocate-memory-laravel-ubuntu>

Vincent Stevenson

[ElasticSearch PHP Client]( https://www.youtube.com/watch?v=Px6nRM_iyQw&ab_channel=VincentStevenson )

ElasticSearch PHP Client

<https://github.com/elastic/elasticsearch-php>

JAVA INSTALL

<https://losst.ru/ustanovka-java-v-ubuntu-18-04>

<https://fornex.com/help/java-ubuntu-16-04/>

<https://stackoverflow.com/questions/52504825/how-to-install-jdk-11-under-ubuntu>	

<https://www.fosstechnix.com/install-elasticsearch-on-ubuntu/>

INSTALL ElasticSearch

<https://www.fosstechnix.com/install-elasticsearch-on-ubuntu/>
											
ElasticSearch Settings possible errors:

<https://stackoverflow.com/questions/58656747/elasticsearch-job-for-elasticsearch-service-failed/58656748>

<https://stackoverflow.com/questions/42549586/elastic-search-give-an-error-no-alive-nodes-found-in-your-cluster>	