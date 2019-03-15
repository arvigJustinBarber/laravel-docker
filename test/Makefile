USER_ID $(shell id -u)

dshell:
    @USER_ID-$(USER_ID) docker-composer run \
        --rm \
        --user $(USER_ID):$(USER_ID) \
        --service-ports \
        --entrypoint=bash \
        php --norc

###

up:
    @php artisan serve --host 0.0.0.0