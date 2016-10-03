FROM camptocamp/odoo-project:10.0-2.3.0
MAINTAINER Camptocamp

# Install additional debian packages if needed
RUN set -x; \
        apt-get update \
        && apt-get install -y --no-install-recommends \
        unzip \
        && apt-get clean \
        && rm -rf /var/lib/apt/lists/*

ADD https://github.com/OCA/odoo-sphinx-autodoc/archive/master.zip .
RUN unzip master.zip

# Install additional python packages if needed
COPY ./requirements.txt /opt/odoo
# install in / to ensure we do not install '-e' packages in a volume
RUN cd /opt/odoo/ && pip install -r requirements.txt

WORKDIR /opt
ENTRYPOINT ["/opt/odoo/bin/docker-entrypoint.sh"]

ENV ADDONS_PATH="/opt/odoo/src/addons, \
  /opt/odoo/external-src/connector, \
  /opt/odoo/external-src/connector-ecommerce, \
  /opt/odoo/external-src/sale-workflow, \
  /opt/odoo/external-src/connector-magento, \
  /opt/odoo/external-src/server-tools, \
  /opt/odoo/external-src/bank-payment, \
  /opt/odoo/external-src/queue, \
  /opt/odoo/external-src/partner-contact" \
  LIMIT_TIME_CPU=86400 \
  LIMIT_TIME_REAL=86400 \
  LIMIT_MEMORY_SOFT=1342177280 \
  LIMIT_MEMORY_HARD=1610612736
