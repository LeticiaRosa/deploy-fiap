# Projeto com Deploy Automático para AWS S3 + CloudFront

> **Forked from [dougls/deploy-fiap](https://github.com/dougls/deploy-fiap)**  
> Projeto desenvolvido para as aulas de deploy de aplicações estáticas com AWS na FIAP.

Este projeto utiliza GitHub Actions para deploy automático de arquivos estáticos para o Amazon S3 e distribuição via CloudFront CDN.

## 🏗️ Arquitetura

- **Amazon S3**: Armazenamento dos arquivos estáticos
- **Amazon CloudFront**: CDN para distribuição global dos arquivos
- **GitHub Actions**: Pipeline de CI/CD para deploy automático

## 🚀 Deploy Automático

O deploy é executado automaticamente a cada push na branch `main` através do GitHub Actions.

### Workflow do Deploy

1. **Checkout do código**: Faz o download do código do repositório
2. **Configuração AWS**: Configura as credenciais da AWS
3. **Sincronização S3**: Faz upload dos arquivos para o bucket S3
4. **Invalidação CDN**: Limpa o cache do CloudFront para refletir as mudanças

## ⚙️ Configuração

### Pré-requisitos

1. Conta AWS ativa
2. Bucket S3 configurado para hospedagem de site estático
3. Distribuição CloudFront apontando para o bucket S3
4. Credenciais AWS com permissões adequadas

### Permissões Necessárias

As credenciais AWS devem ter as seguintes permissões:

```json
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": [
                "s3:DeleteObject",
                "s3:GetObject",
                "s3:PutObject",
                "s3:ListBucket"
            ],
            "Resource": [
                "arn:aws:s3:::seu-bucket-name",
                "arn:aws:s3:::seu-bucket-name/*"
            ]
        },
        {
            "Effect": "Allow",
            "Action": [
                "cloudfront:CreateInvalidation"
            ],
            "Resource": "*"
        }
    ]
}
```

### Secrets do GitHub

Configure os seguintes secrets no seu repositório GitHub:

| Secret | Descrição |
|--------|-----------|
| `AWS_ACCESSKEY` | Access Key ID da AWS |
| `AWS_SECRETKEY` | Secret Access Key da AWS |
| `BUCKETNAME` | Nome do bucket S3 |
| `CDN_ID` | ID da distribuição CloudFront |

**Como configurar secrets:**
1. Vá para o repositório no GitHub
2. Clique em `Settings` → `Secrets and variables` → `Actions`
3. Clique em `New repository secret`
4. Adicione cada secret com seu respectivo valor

## 📁 Estrutura do Projeto

```
.
├── .github/
│   └── workflows/
│       └── deploy.yml      # Workflow do GitHub Actions
├── src/                    # Seus arquivos de código
├── assets/                 # Arquivos estáticos (imagens, CSS, JS)
├── index.html             # Página principal
└── README.md              # Este arquivo
```

## 🔄 Processo de Deploy

### Automático
- Faça um push para a branch `main`
- O GitHub Actions será executado automaticamente
- Os arquivos serão sincronizados com o S3
- O cache do CloudFront será invalidado

### Manual (via AWS CLI)
```bash
# Sincronizar arquivos
aws s3 sync . s3://seu-bucket-name --delete --exclude ".git/*" --exclude ".github/*" --exclude "README.md"

# Invalidar cache
aws cloudfront create-invalidation --distribution-id SEU_CDN_ID --paths "/*"
```

## 📊 Monitoramento

Você pode monitorar o status dos deploys em:
- **GitHub Actions**: Na aba `Actions` do repositório
- **AWS S3**: Console da AWS → S3 → seu bucket
- **CloudFront**: Console da AWS → CloudFront → sua distribuição

## 🛠️ Customizações

### Excluir outros arquivos do upload
Edite o comando `aws s3 sync` no workflow para excluir arquivos adicionais:

```yaml
aws s3 sync . s3://${{ secrets.BUCKETNAME }} --delete \
  --exclude ".git/*" \
  --exclude ".github/*" \
  --exclude "README.md" \
  --exclude "*.log" \
  --exclude "node_modules/*"
```

### Deploy para outras branches
Para fazer deploy de outras branches, edite o arquivo `.github/workflows/deploy.yml`:

```yaml
on:
  push:
    branches:
      - main
      - develop
      - staging
```

### Diferentes ambientes
Para configurar diferentes ambientes (dev, staging, prod), você pode usar diferentes secrets baseados na branch:

```yaml
- name: Set environment variables
  run: |
    if [[ ${{ github.ref }} == 'refs/heads/main' ]]; then
      echo "BUCKET_NAME=${{ secrets.PROD_BUCKETNAME }}" >> $GITHUB_ENV
      echo "CDN_ID=${{ secrets.PROD_CDN_ID }}" >> $GITHUB_ENV
    else
      echo "BUCKET_NAME=${{ secrets.DEV_BUCKETNAME }}" >> $GITHUB_ENV
      echo "CDN_ID=${{ secrets.DEV_CDN_ID }}" >> $GITHUB_ENV
    fi
```

## 🔒 Segurança

- ✅ As credenciais AWS são armazenadas como secrets do GitHub
- ✅ O workflow usa a versão mais recente das actions
- ✅ Permissões mínimas necessárias para as credenciais AWS
- ✅ Exclusão de arquivos sensíveis do upload (.git, .github)

## 📝 Logs e Troubleshooting

### Problemas Comuns

1. **Erro de permissão AWS**: Verifique se as credenciais têm as permissões necessárias
2. **Bucket não encontrado**: Confirme se o nome do bucket está correto nos secrets
3. **CDN não atualiza**: A invalidação pode levar alguns minutos para propagar

### Debug
Para debugar problemas, adicione logs no workflow:

```yaml
- name: Debug AWS Configuration
  run: |
    aws sts get-caller-identity
    aws s3 ls s3://${{ secrets.BUCKETNAME }}
```

## 📞 Suporte

Para dúvidas ou problemas:
1. Verifique os logs do GitHub Actions
2. Consulte a documentação da AWS
3. Abra uma issue neste repositório

## 🎓 Sobre o Projeto

Este projeto foi desenvolvido como material educacional para as aulas de **Deploy de Aplicações Estáticas com AWS** na FIAP. O objetivo é demonstrar na prática como implementar um pipeline de CI/CD usando GitHub Actions para automatizar o deploy de sites estáticos na infraestrutura da AWS.

### Conceitos Abordados
- Integração Contínua e Deploy Contínuo (CI/CD)
- Armazenamento na nuvem com Amazon S3
- Content Delivery Network (CDN) com CloudFront
- Automação com GitHub Actions
- Gerenciamento de secrets e segurança

### Projeto Original
Este repositório é um fork de [dougls/deploy-fiap](https://github.com/dougls/deploy-fiap), adaptado e documentado para fins educacionais.

---

**Desenvolvido com ❤️ usando AWS e GitHub Actions para a FIAP**
