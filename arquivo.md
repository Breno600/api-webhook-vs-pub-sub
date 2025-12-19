kubectl -n omnidata exec -it pod/transaction-rules-v2-dev-java-chart-688b86cc7f-fclcq -- sh -lc '
ls -lah /opt/booter/snowflake-certs /opt/booter/kafka-certs;
wc -c /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8;
wc -c /opt/booter/kafka-certs/*.p12 2>/dev/null || true
'
