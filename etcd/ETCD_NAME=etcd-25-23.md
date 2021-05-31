ETCD_NAME=etcd-25-23
ETCD_DATA_DIR="/var/lib/etcd/etcd-cluster"
ETCD_LISTEN_PEER_URLS="http://10.39.25.23:2380"

ETCD_LISTEN_CLIENT_URLS="http://10.39.25.23:2379,http://127.0.0.1:2379"
    ETCD_INITIAL_ADVERTISE_PEER_URLS="http://10.39.25.23:2380"
    ETCD_INITIAL_CLUSTER="etcd-25-23=http://10.39.25.23:2380,etcd-25-24=http://10.39.25.24:2380,etcd-25-25=http://10.39.25.25:2380"
    ETCD_INITIAL_CLUSTER_STATE="new"
    ETCD_INITIAL_CLUSTER_TOKEN="etcd-cluster"
    ETCD_ADVERTISE_CLIENT_URLS="http://10.39.25.23:2379"