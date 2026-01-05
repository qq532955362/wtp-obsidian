 ```sh
 docker restart \
 $(docker ps -q \
--filter name=atoto-erp-flowable \
--filter name=atoto-mall-index \
--filter name=atoto-mall-product \
--filter name=atoto-product-core \
--filter name=atoto-product-ibook \
--filter name=atoto-product-search \
--filter name=atoto-software-upgrade \
--filter name=atoto-user \
--filter name=atoto-knowledge-chat \
--filter name=atoto-file-center \
--filter name=atoto-gps-core \
--filter name=atoto-data-sync \
--filter name=atoto-point-service \
--filter name=atoto-common-file \
--filter name=atoto-erp-core \
--filter name=atoto-pricing-system \
--filter name=atoto-data-crawler \
--filter name=atoto-driving-recorder \
--filter name=drivechat \
--filter name=atoto-sandbox)
 
 ```
