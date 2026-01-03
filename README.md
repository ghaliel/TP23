# 🔄 Migration de Eureka vers Consul

## 📋 Description

Ce projet démontre la **migration d'un système de découverte de services** de **Netflix Eureka** vers **HashiCorp Consul**. Il illustre les différences entre les deux solutions et fournit une architecture microservices fonctionnelle avec chaque système.

Le projet permet de comparer les fonctionnalités, la configuration et les performances des deux plateformes de service discovery les plus populaires dans l'écosystème Spring Cloud.

## 🏗️ Architecture

Architecture microservices avec service discovery interchangeable :

- **Microservices** : Services métier enregistrés auprès du discovery server
- **Discovery Server** : Eureka (8761) ou Consul (8500)
- **API Gateway** : Point d'entrée avec résolution de services dynamique

## 🚀 Technologies

### Service Discovery
- **Netflix Eureka** : Solution Spring Cloud native
- **HashiCorp Consul** : Plateforme complète (Discovery + KV Store + Health Checks)

### Backend
- **Spring Boot** : Framework principal
- **Spring Cloud** : Intégration Eureka/Consul
- **Spring Cloud Gateway** : API Gateway

## 📊 Comparaison Eureka vs Consul

| Critère | Eureka | Consul |
|---------|--------|--------|
| **Type** | Service Discovery uniquement | Plateforme complète |
| **Port par défaut** | 8761 | 8500 |
| **UI Web** | ✅ Basique | ✅ Avancée |
| **Health Checks** | ✅ Basique (Heartbeat) | ✅ Avancés (HTTP, TCP, Script) |
| **Key/Value Store** | ❌ | ✅ |
| **Multi-Datacenter** | ❌ | ✅ |
| **Consensus** | Peer-to-Peer | Raft (plus robuste) |
| **Configuration** | Simple | Plus complexe mais plus puissant |

## 📦 Prérequis

- Java 17 ou supérieur
- Maven 3.6+
- Docker & Docker Compose (pour Consul)

## ⚙️ Installation

### Option 1 : Avec Eureka

1. **Démarrer Eureka Server**
```bash
cd eureka-server
mvn spring-boot:run
```

Accéder au dashboard : http://localhost:8761

2. **Démarrer les Microservices**
```bash
# Configuration avec Eureka active
mvn spring-boot:run -Dspring.profiles.active=eureka
```

### Option 2 : Avec Consul

1. **Démarrer Consul avec Docker**
```bash
docker run -d -p 8500:8500 -p 8600:8600/udp --name=consul consul agent -server -ui -bootstrap-expect=1 -client=0.0.0.0
```

Accéder au dashboard : http://localhost:8500

2. **Démarrer les Microservices**
```bash
# Configuration avec Consul active
mvn spring-boot:run -Dspring.profiles.active=consul
```

## 🏃 Utilisation

### Vérifier l'Enregistrement des Services

**Avec Eureka :**
- Dashboard : http://localhost:8761
- API : `GET http://localhost:8761/eureka/apps`

**Avec Consul :**
- Dashboard : http://localhost:8500/ui
- API : `GET http://localhost:8500/v1/catalog/services`

### Tester la Résolution de Services

Les microservices se découvrent automatiquement via le discovery server configuré.

## 🔧 Configuration

### Configuration Eureka (`application-eureka.yml`)

```yaml
eureka:
  client:
    service-url:
      defaultZone: http://localhost:8761/eureka/
    register-with-eureka: true
    fetch-registry: true
  instance:
    prefer-ip-address: true
```

### Configuration Consul (`application-consul.yml`)

```yaml
spring:
  cloud:
    consul:
      host: localhost
      port: 8500
      discovery:
        enabled: true
        register: true
        health-check-path: /actuator/health
        health-check-interval: 10s
```

## 🎯 Points Clés de la Migration

### 1. Dépendances Maven

**Eureka :**
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-netflix-eureka-client</artifactId>
</dependency>
```

**Consul :**
```xml
<dependency>
    <groupId>org.springframework.cloud</groupId>
    <artifactId>spring-cloud-starter-consul-discovery</artifactId>
</dependency>
```

### 2. Annotations

**Eureka :**
```java
@EnableEurekaClient  // ou @EnableDiscoveryClient
```

**Consul :**
```java
@EnableDiscoveryClient
```

### 3. Health Checks

- **Eureka** : Heartbeat simple (30s par défaut)
- **Consul** : Health checks HTTP configurables avec Spring Actuator

## 📈 Avantages de Consul

### ✅ Fonctionnalités Supplémentaires

1. **Key/Value Store** : Configuration distribuée
2. **Health Checks Avancés** : HTTP, TCP, Scripts personnalisés
3. **Multi-Datacenter** : Support natif
4. **Service Mesh** : Consul Connect pour sécurité mTLS
5. **DNS Interface** : Résolution via DNS standard

### ✅ Production-Ready

- Consensus Raft pour haute disponibilité
- ACLs pour sécurité
- Monitoring et métriques intégrés

## 🎓 Concepts Démontrés

- ✅ Service Discovery avec Eureka
- ✅ Service Discovery avec Consul
- ✅ Migration entre systèmes de discovery
- ✅ Configuration multi-profils Spring
- ✅ Health Checks et monitoring
- ✅ Load balancing côté client

## 🔄 Quand Choisir Quoi ?

### Choisir Eureka si :
- ✅ Vous êtes déjà dans l'écosystème Spring Cloud Netflix
- ✅ Vous avez besoin uniquement de service discovery
- ✅ Vous voulez une solution simple et rapide

### Choisir Consul si :
- ✅ Vous avez besoin de fonctionnalités avancées (KV Store, Service Mesh)
- ✅ Vous gérez plusieurs datacenters
- ✅ Vous voulez des health checks sophistiqués
- ✅ Vous prévoyez une architecture multi-cloud

## 📚 Documentation

- [Spring Cloud Netflix Eureka](https://spring.io/projects/spring-cloud-netflix)
- [Spring Cloud Consul](https://spring.io/projects/spring-cloud-consul)
- [HashiCorp Consul](https://www.consul.io/)

## 🚀 Évolutions Possibles

- Implémentation de Consul Connect (Service Mesh)
- Utilisation du KV Store pour configuration distribuée
- Multi-datacenter setup
- ACLs et sécurité avancée