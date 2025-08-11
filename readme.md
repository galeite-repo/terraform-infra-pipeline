# Projeto Terraform Infra Pipeline

Este projeto gerencia infraestrutura AWS utilizando Terraform, com automação via GitHub Actions para ambientes de desenvolvimento (`dev`) e produção (`prod`).

## Estrutura

- **infra/**: Diretório principal dos arquivos Terraform.
  - `main.tf`: Criação do bucket S3.
  - `provider.tf`: Configuração do provider AWS (região: sa-east-1).
  - `variables.tf`: Variáveis utilizadas no projeto.
  - `backend.tf`: Configuração do backend remoto S3 para o state do Terraform.
  - `destroy_config.json`: Define se o ambiente será destruído ou mantido no pipeline.
  - **envs/**: Contém variáveis específicas para cada ambiente (`dev` e `prod`).

- **.github/workflows/**: Workflows do GitHub Actions para CI/CD.
  - `develop.yml`: Pipeline para o branch `develop` (ambiente dev).
  - `main.yml`: Pipeline para o branch `main` (ambiente prod).
  - `terraform.yml`: Workflow reutilizável para execução do Terraform.

## Como funciona

- Ao realizar push nos branches `main` ou `develop`, o GitHub Actions executa o workflow correspondente.
- O pipeline realiza:
  1. Checkout do código
  2. Configuração das credenciais AWS
  3. Leitura do arquivo `destroy_config.json` para decidir entre `destroy` ou `apply`
  4. Execução dos comandos Terraform (`init`, `validate`, `plan`, `apply` ou `destroy`)
- O state do Terraform é armazenado em um bucket S3 e bloqueado via DynamoDB.

## Requisitos

- Conta AWS com permissões para criar buckets S3 e tabelas DynamoDB.
- Roles configuradas para uso via GitHub Actions.
- Terraform >= 1.8.3

## Como executar localmente

```sh
cd infra
terraform init
terraform workspace select dev || terraform workspace new dev
terraform plan -var-file="./envs/dev/terraform.tfvars"
terraform apply -var-file="./envs/dev/terraform.tfvars"
```

## Observações

- O arquivo `.gitignore` ignora a pasta `.terraform`.
- O pipeline pode destruir recursos automaticamente conforme definido em `destroy_config.json`.

---

Qualquer dúvida, consulte os workflows em [.github/workflows/](.github/workflows)