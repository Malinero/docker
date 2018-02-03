
ODOO_MAJOR ?= 11
IMAGE ?= odoo:${ODOO_MAJOR}
NAME ?= openeducat${ODOO_MAJOR}
BIND_PORT ?= 8097
# Postgresql
PG_DB_USER ?= odoo
PG_DB_NAME ?= odoo
PG_DB_PASS ?= odoo
PG_VERSION ?= 9.4
PG_HOST ?= openeducat${ODOO_MAJOR}-postgres

backup:
	curl -X POST  http://127.0.0.1:${BIND_PORT}/web/database/backup -d "master_pwd=secret&name=Test&backup_format=zip" -o test.zip

run: run_postgresql run_openeducat
run_postgresql:
	docker run -i -d ${DOCKER_NETWORK} \
                -e POSTGRES_USER=${PG_DB_USER} \
                -e POSTGRES_DB=${PG_DB_NAME} \
                -e POSTGRES_PASSWORD=${PG_DB_PASS} \
                --name ${PG_HOST} \
                postgres:${PG_VERSION}
run_openeducat:
	git clone https://github.com/openeducat/openeducat_erp.git -b ${ODOO_MAJOR}.0 
	docker run \
		-v ${PWD}/openeducat_erp:/mnt/extra-addons \
		-p ${BIND_PORT}:8069 \
		--name openeducat${ODOO_MAJOR} \
		--link ${PG_HOST}:db \
		-i -d ${IMAGE}

stop:
	docker stop openeducat${ODOO_MAJOR} ${PG_HOST}
destroy: stop
	rm -rf ${PWD}/openeducat_erp
	docker rm -v openeducat${ODOO_MAJOR} ${PG_HOST}
