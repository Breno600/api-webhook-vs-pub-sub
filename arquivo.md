api-webhook-vs-pub-sub $ kubectl -n omnidata exec -it pod/transaction-rules-v2-dev-java-chart-688b86cc7f-qqz2t -- sh -lc '
ls -lah /opt/booter/snowflake-certs /opt/booter/kafka-certs;
wc -c /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8;
wc -c /opt/booter/kafka-certs/*.p12 2>/dev/null || true
'
/opt/booter/kafka-certs:
total 0
drwxrwxrwt. 3 root   root   120 Dec 19 17:58 .
drwxrwxrwx. 1 booter booter  60 Dec 19 17:58 ..
drwxr-xr-x. 2 root   root    80 Dec 19 17:58 ..2025_12_19_20_58_00.1484936582
lrwxrwxrwx. 1 root   root    32 Dec 19 17:58 ..data -> ..2025_12_19_20_58_00.1484936582
lrwxrwxrwx. 1 root   root    27 Dec 19 17:58 kafka-truststore.p12 -> ..data/kafka-truststore.p12
lrwxrwxrwx. 1 root   root    39 Dec 19 17:58 wvc-ap11753-me-kpc-d.1dc.com.p12 -> ..data/wvc-ap11753-me-kpc-d.1dc.com.p12

/opt/booter/snowflake-certs:
total 0
drwxrwxrwt. 3 root   root   100 Dec 19 17:58 .
drwxrwxrwx. 1 booter booter  60 Dec 19 17:58 ..
drwxr-xr-x. 2 root   root    60 Dec 19 17:58 ..2025_12_19_20_58_00.3031531947
lrwxrwxrwx. 1 root   root    32 Dec 19 17:58 ..data -> ..2025_12_19_20_58_00.3031531947
lrwxrwxrwx. 1 root   root    40 Dec 19 17:58 OMNIDATA_LOWER_SRVACCT_rsa_key.p8 -> ..data/OMNIDATA_LOWER_SRVACCT_rsa_key.p8
1892 /opt/booter/snowflake-certs/OMNIDATA_LOWER_SRVACCT_rsa_key.p8
 6467 /opt/booter/kafka-certs/kafka-truststore.p12
 5863 /opt/booter/kafka-certs/wvc-ap11753-me-kpc-d.1dc.com.p12
12330 total
api-webhook-vs-pub-sub $ 
