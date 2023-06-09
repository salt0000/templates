create-project: # Create project
	mkdir api
	@make build
	@make up
	docker-compose exec php composer create-project laravel/laravel . "${version}"
	docker-compose exec php chmod -R 777 storage bootstrap/cache
set-up: # Set up app after git clone
	cp ./api/.env.example ./api/.env
	@make build
	@make up
	@make install
	# @make seed


# Commands for container
build:
	docker-compose build
up:
	docker-compose up -d
down:
	docker-compose down
restart:
	@make down
	@make build
	@make up
nginx:
	docker-compose exec nginx sh
php:
	docker-compose exec php bash
mysql:
	docker-compose exec mysql bash


# Commands for development
crud:
	docker-compose exec php php artisan make:model ${name} --factory --controller --api --requests  --policy
controller-invoke:
	docker-compose exec php php artisan make:controller ${name}Controller --invokable
controller:
	docker-compose exec php php artisan make:controller ${name}Controller --api
mail:
	docker-compose exec php php artisan make:mail ${name}Mail
provider:	
	docker-compose exec php php artisan make:provider ${name}Provider
middleware:	
	docker-compose exec php php artisan make:middleware ${name}
model:
	docker-compose exec php php artisan make:model ${name}
request:
	docker-compose exec php php artisan make:request ${name}Request
resource:
	docker-compose exec php php artisan make:resource ${name}Resource
policy:
	docker-compose exec php php artisan make:policy ${name}Policy --model=${name}
migration:
	docker-compose exec php php artisan make:migration create_${name}_table
factory:
	docker-compose exec php php artisan make:factory ${name}Factory
seeder:
	docker-compose exec php php artisan make:seeder ${name}Seeder
migrate:
	docker-compose exec php php artisan migrate:fresh
seed:
	docker-compose exec php php artisan db:seed
route:
	docker-compose exec php php artisan route:list --except-vendor
route-vendor:
	docker-compose exec php php artisan route:list --only-vendor
tinker:
	docker-compose exec php php artisan tinker
about:
	docker-compose exec php php artisan about
install:
	docker-compose exec php composer install
require:
	docker-compose exec php composer require ${name}
require-dev:
	docker-compose exec php composer require ${name} --dev


# Commands for altas
inspect:
	docker run --network=template-backend_default --rm -it arigaio/atlas:latest schema inspect -u "mysql://root:secret@mysql:3306/template" --format "{{ sql . }}" > docker/mysql/initdb.d/schema.sql
# create dev table in advance
apply:
	docker run --network=template-backend_default --rm -v "$$PWD/docker/mysql/initdb.d/schema.sql:/schema.sql" -it arigaio/atlas:latest schema apply -u "mysql://root:secret@mysql:3306/template" --to "file://schema.sql" --dev-url "mysql://root:secret@mysql:3306/dev"
schemaspy:
	docker run --network=template-backend_default --rm -v "$$PWD/schemaspy_output:/output" schemaspy/schemaspy:latest -t mysql -host mysql -port 3306 -db template -s template -u root -p secret -connprops useSSL\\=false


up-prod:
	docker build -f docker/nginx/prod/Dockerfile -t nginx .
	docker build -f docker/php/prod/Dockerfile -t php .
	docker build -f docker/mysql/prod/Dockerfile -t mysql .
	docker network create prod
	docker run --network=prod --rm -d --name php php:latest
	docker run --network=prod --rm -d --name nginx -p 80:80 nginx:latest
	docker run --network=prod --rm -d --name mysql mysql:latest
down-prod:
	docker stop php
	docker stop nginx
	docker stop mysql
	docker network rm prod
	docker image rm php:latest
	docker image rm nginx:latest
	docker image rm mysql:latest