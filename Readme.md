# Sample Project 4 inspec

Démontre comment utiliser inspec au sein d'un pipeline Jenkins pour analyser un ensemble de serveurs

## Usage

Définir la liste des serveurs dans le fichier inventaires du projet sécurité. Ce projet doit être appelé dans un JOB séparé. Le `jenkinsfile` mis dans ce projet est celui qui va analyse l'inventaire et exécuter les tests.

```yaml
- remote: true
  ssh: devops:devops@192.168.33.37
  ip: 192.168.33.37
  baselines:
    - project: https://github.com/dev-sec/linux-baseline.git
      dir: linux-baseline
    - project: https://github.com/dev-sec/ssl-baseline.git
      dir: ssl-baseline
    - project: https://github.com/dev-sec/nginx-baseline.git
      dir: nginx-baseline
    - project: https://github.com/dev-sec/postgres-baseline.git
      dir: postgres-baseline

- remote: false
  ip: 192.168.33.200
  baselines:
    - project: https://github.com/dev-sec/linux-baseline.git
      dir: linux-baseline
    - project: https://github.com/dev-sec/ssl-baseline.git
      dir: ssl-baseline
    - project: https://github.com/dev-sec/nginx-baseline.git
      dir: nginx-baseline
    - project: https://github.com/dev-sec/postgres-baseline.git
      dir: postgres-baseline
```

## Exemple d'analyse

![Exemple d'analyse avec le plugin digitl ocean](/docs/analyse.PNG "Exemple d'analyse avec le plugin digitl ocean")

![Exemple d'analyse avec le plugin Junit](/docs/analyse2.PNG "Exemple d'analyse avec le plugin digitl ocean")
