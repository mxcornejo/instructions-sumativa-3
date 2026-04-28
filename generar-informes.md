# Generación de Informes

## SonarQube

1. Ejecutar SonarQube en Docker.
2. Luego, en la terminal del proyecto **frontend** y del proyecto **backend**, ejecutar:

```bash
mvn sonar:sonar -Dsonar.token={TU_TOKEN}
```

## JaCoCo

1. Ejecutar en la terminal de cada proyecto:

```bash
mvn verify
```

2. Abrir el informe HTML generado en:

```
target/site/jacoco/index.html
```

> Repetir este paso en ambos proyectos (frontend y backend).
