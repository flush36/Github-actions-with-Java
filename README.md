# Github Actions with Java project

Exemplo de aplicação Java com Maven, JUnit e pipeline CI/CD no GitHub Actions.



# CI/CD Pipeline Java — GitHub Actions

## Definição do pipeline

```yaml
name: CI/CD Pipeline Java
```
- Nome do pipeline.

```yaml
on:
  push:
    branches:
      - main
      - develop
      - 'release/*'
      - 'hotfix/*'
      - 'feature/*'
  pull_request:
    branches:
      - develop
      - main
```
- Dispara no push e pull request dessas branches.

## Job: Build

```yaml
jobs:
  build:
    name: Build and Test
    runs-on: ubuntu-latest
```
- Cria o job de build que roda no Ubuntu.

```yaml
steps:
- name: Checkout repository
  uses: actions/checkout@v3
```
- Faz o checkout do código.

```yaml
- name: Set up Java
  uses: actions/setup-java@v3
  with:
    distribution: 'temurin'
    java-version: '17'
```
- Instala Java 17 (Temurin).

```yaml
- name: Cache Maven packages
  uses: actions/cache@v3
  with:
    path: ~/.m2
    key: ${{ runner.os }}-maven-${{ hashFiles('**/pom.xml') }}
    restore-keys: |
      ${{ runner.os }}-maven-
```
- Cria cache das dependências Maven.

```yaml
- name: Build with Maven
  run: mvn -B package --file pom.xml
```
- Executa o build Maven.

```yaml
- name: Run tests
  run: mvn test
```
- Executa os testes.

## Job: Deploy

```yaml
deploy:
  name: Deploy artifact
  runs-on: ubuntu-latest
  needs: build
  if: github.ref == 'refs/heads/main'
```
- Só executa se estiver na branch `main` e após o build.

```yaml
steps:
- name: Checkout repository
  uses: actions/checkout@v3
```
- Faz o checkout.

```yaml
- name: Set up Java
  uses: actions/setup-java@v3
  with:
    distribution: 'temurin'
    java-version: '17'
```
- Instala Java 17.

```yaml
- name: Build with Maven
  run: mvn -B package --file pom.xml
```
- Executa o build.

```yaml
- name: Upload artifact
  uses: actions/upload-artifact@v3
  with:
    name: simple-java-app
    path: target/*.jar
```
- Faz upload do arquivo `.jar` como artefato.

