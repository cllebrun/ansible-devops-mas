kafka
=====

This role provides support to install a Kafka Cluster using [Red Hat AMQ Streams](https://www.redhat.com/en/resources/amq-streams-datasheet) and generate configuration that can be directly applied to Maximo Application Suite.

> The Red Hat AMQ streams component is a massively scalable, distributed, and high-performance data streaming platform based on the Apache Kafka project. It offers a distributed backbone that allows microservices and other applications to share data with high throughput and low latency.
>
> As more applications move to Kubernetes and Red Hat OpenShift, it is increasingly important to be able to run the communication infrastructure on the same platform. Red Hat OpenShift, as a highly scalable platform, is a natural fit for messaging technologies such as Kafka. The AMQ streams component makes running and managing Apache Kafka OpenShift native through the use of powerful operators that simplify the deployment, configuration, management, and use of Apache Kafka on Red Hat OpenShift.
>
> The AMQ streams component is part of the Red Hat AMQ family, which also includes the AMQ broker, a longtime innovation leader in Java™ Message Service (JMS) and polyglot messaging, as well as the AMQ interconnect router, a wide-area, peer-to-peer messaging solution.


!!! tip
    The role will generate a yaml file containing the definition of a Secret and KafkaCfg resource that can be used to configure the deployed cluster as the MAS system Kafka.

    This file can be directly applied using `oc apply -f $MAS_CONFIG_DIR/kafkacfg-amqstreams-system.yaml` or used in conjunction with the [suite_config](suite_config.md) role.

Role Variables
--------------

### kafka_version
The version of Kafka to deploy by the operator. Before changing the kafka_version make the version is supported
by the [amq-streams operator version](https://access.redhat.com/documentation/en-us/red_hat_amq_streams).

- Environment Variable: `KAFKA_VERSION`
- Default Value: `2.7.0`

### kafka_namespace
The namespace where the operator and Kafka cluster will be deployed.

- Environment Variable: `KAFKA_NAMESPACE`
- Default Value: `amq-streams`

### kafka_cluster_name
The name of the Kafka cluster that will be created

- Environment Variable: `KAFKA_CLUSTER_NAME`
- Default Value: `maskafka`

### kafka_cluster_size
The configuration to apply, there are two configurations available: small and large.

- Environment Variable: `KAFKA_CLUSTER_SIZE`
- Default Value: `small`

### kafka_storage_class
The name of the storage class to configure the AMQStreams operator to use for persistent storage in the Kafka cluster.

- Environment Variable: `KAFKA_STORAGE_CLASS`
- Default Value: lookup supported storage classes in the cluster

### kafka_storage_size
The size of the storage to configure the AMQStreams operator to use for persistent storage in the Kafka cluster.

- Environment Variable: `KAFKA_STORAGE_SIZE`
- Default Value: `100Gi`

### zookeeper_storage_class
The name of the storage class to configure the AMQStreams operator to use for persistent storage in the Zookeeper cluster.

- Environment Variable: `ZOOKEEPER_STORAGE_CLASS`
- Default Value: lookup supported storage classes in the cluster

### zookeeper_storage_size
The size of the storage to configure the AMQStreams operator to use for persistent storage in the Zookeeper cluster.

- Environment Variable: `ZOOKEEPER_STORAGE_SIZE`
- Default Value: `10Gi`

### kafka_user_name
The name of the user to setup in the cluster for MAS.

- Environment Variable: `KAFKA_USER_NAME`
- Default Value: `masuser`

### kafka_user_password (supported in Strimzi operator verion [0.25.0](https://github.com/strimzi/strimzi-kafka-operator/blob/main/CHANGELOG.md#0250) - amq streams operator version 2.x)
The password of the user to setup in the cluster for MAS.

- Environment Variable: `KAFKA_USER_PASSWORD`
- Default Value: a randomly generated password is used if one is not specified

### mas_instance_id
The instance ID of Maximo Application Suite that the KafkaCfg configuration will target.  If this or `mas_config_dir` are not set then the role will not generate a KafkaCfg template.

- Environment Variable: `MAS_INSTANCE_ID`
- Default Value: None

### mas_config_dir
Local directory to save the generated KafkaCfg resource definition.  This can be used to manually configure a MAS instance to connect to the Kafka cluster, or used as an input to the [suite_config](suite_config.md) role. If this or `mas_instance_id` are not set then the role will not generate a KafkaCfg template.

- Environment Variable: `MAS_CONFIG_DIR`
- Default Value: None


Example Playbook
----------------

```yaml
- hosts: localhost
  any_errors_fatal: true
  vars:
    # Set storage class suitable for use on IBM Cloud ROKS
    kafka_storage_class: ibmc-block-gold

    # Generate a KafkaCfg template
    mas_instance_id: masinst1
    mas_config_dir: ~/masconfig
  roles:
    - ibm.mas_devops.kafka
```

License
-------

EPL-2.0
