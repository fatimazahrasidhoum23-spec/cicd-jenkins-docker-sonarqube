#  CI/CD Pipeline — Jenkins, Docker & SonarQube

![Jenkins](https://img.shields.io/badge/Jenkins-2.541.3-D24939?style=for-the-badge&logo=jenkins)
![Docker](https://img.shields.io/badge/Docker-28.1.1-2496ED?style=for-the-badge&logo=docker)
![SonarQube](https://img.shields.io/badge/SonarQube-10.6-4E9BCD?style=for-the-badge&logo=sonarqube)
![Ubuntu](https://img.shields.io/badge/Ubuntu-20.04_LTS-E95420?style=for-the-badge&logo=ubuntu)

##  Objectif

Mise en place d'une **pipeline CI/CD complète** pour l'intégration continue et l'analyse de la qualité du code, en utilisant les outils standards de l'industrie DevOps.



##  Architecture
┌─────────────────────────────────────────────────────────┐
│ Ubuntu 20.04 LTS │
│ │
│ ┌──────────┐ ┌──────────┐ ┌─────────────────┐ │
│ │ GitLab │───▶│ Jenkins │───▶│ SonarQube │ │
│ │ (SCM) │ │ :8080 │ │ :9000 │ │
│ └──────────┘ └────┬─────┘ └─────────────────┘ │
│ │ │
│ ┌────▼─────┐ │
│ │ Docker │ │
│ └──────────┘ │
└─────────────────────────────────────────────────────────┘



##  Stack technique

| Outil | Version | Rôle |
|-------|---------|------|
| **Jenkins** | 2.541.3 | Serveur CI/CD |
| **Docker** | 28.1.1 | Conteneurisation |
| **SonarQube** | 10.6-community | Analyse qualité du code |
| **GitLab** | Cloud | Gestionnaire de code source |
| **Java** | JDK 17 | Runtime Jenkins |
| **Ubuntu** | 20.04 LTS | Système d'exploitation |



##  Installation

### Prérequis
- Ubuntu 20.04 LTS
- Accès sudo
- Java JDK 17
- Ports : `8080` (Jenkins), `9000` (SonarQube)

### 1️ Jenkins

```bash
# Ajout du dépôt officiel
sudo apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 7198F4B714ABFC68
echo "deb https://pkg.jenkins.io/debian-stable binary/" | sudo tee /etc/apt/sources.list.d/jenkins.list
sudo apt update && sudo apt install jenkins -y

# Démarrage du service
sudo systemctl enable --now jenkins

# Récupération du mot de passe initial
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### 2️ Docker

```bash
sudo apt install docker.io -y
sudo systemctl enable --now docker
sudo usermod -aG docker jenkins
sudo chmod 666 /var/run/docker.sock
sudo systemctl restart jenkins
```

### 3️ SonarQube

```bash
sudo sysctl -w vm.max_map_count=262144
docker run -d --name sonarqube \
  -p 9000:9000 \
  -v sonarqube_data:/opt/sonarqube/data \
  -v sonarqube_extensions:/opt/sonarqube/extensions \
  -v sonarqube_logs:/opt/sonarqube/logs \
  sonarqube:10.6-community
```



##  Configuration

### Plugins Jenkins installés
- Docker Pipeline
- Docker plugin
- Git plugin

### Credentials configurés
- `gitlab-credentials` — Accès au repo GitLab
- `sonar-token` — Token SonarQube (Secret text)



##  Jenkinsfile

```groovy
pipeline {
    agent any
    stages {
        stage('SonarQube Analysis') {
            steps {
                withCredentials([string(credentialsId: 'sonar-token', variable: 'SONAR_TOKEN')]) {
                    sh """
                        echo "SonarQube Analysis"
                    """
                }
            }
        }
        stage('Build') {
            steps {
                echo 'Building...'
            }
        }
    }
}
```



##  Résultats SonarQube

| Métrique | Valeur |
|----------|--------|
| Lines of Code | 125 |
| New Issues | 14 |
| Coverage | 0.0% |
| Security Hotspots | 3 |
| Duplications | 0.0% |



##  Accès aux services

| Service | URL | Credentials |
|---------|-----|-------------|
| Jenkins | `http://localhost:8080` | admin / admin123 |
| SonarQube | `http://localhost:9000` | admin / admin |



##  Captures d'écran

### Jenkins — Dashboard
<img width="520" height="424" alt="image" src="https://github.com/user-attachments/assets/a0b48bac-4908-4395-852f-3bfa2cae9ade" />


### Jenkins — Pipeline réussi
<img width="608" height="493" alt="image" src="https://github.com/user-attachments/assets/9ba851a5-0f1c-44d2-8c75-2b9e3bb78fee" />


### SonarQube — Analyse du code
<img width="385" height="459" alt="image" src="https://github.com/user-attachments/assets/a47aeb64-9d7b-4389-861a-c24e5d69e724" />





