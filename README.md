## Descrição

Este repositório é destinado a exemplos de:
- Experimentação e tracking de treinamentos de modelos usando MLFlow
- Criação de serviços para modelos usando o BentoML
- Deploy dos serviços usando o github action (By Steps e One Step)

## Arquivos de Exemplo

#### ```0 - Treinando e Trackeando modelos com MLFlow.ipynb```

Notebook com exemplo de uso do MLFlow, na etapa de treinamento do modelo, com Autolog e com log personalizado.

#### ```1 - Servindo modelo com MLFlow e BentoML.ipynb```     

Notebook com exemplo de criação de um serviço com o BentoML a partir de um modelo treinado usando MLFlow como ferramenta de tracking.

#### ```2 - Servindo modelo Pickle com BentoML.ipynb```

Notebook com exemplo de criação de um serviço com o BentoML a partir de um modelo serializado com Pickle local.

#### ```3 - Treinando e servindo com BentoML.ipynb```    

Notebook com exemplo de criação de um serviço com o BentoML a partir de um treinamento direto usando sklearn.

#### ```4 - Servindo modelo salvo no MLFlow Log Artifact com BentoML.ipynb```  

Notebook com exemplo de criação de um serviço com o BentoML a partir de um modelo serializado com pickle e logado como artifact no MLFlow.

#### ```Criar ENV com Pipenv.ipynb```

Exemplo de criação de ambiente usando Pipenv

## Deploy de serviços com modelos

```deploy-prod.yml``` - Realiza o deploy em apenas um passo usando a action [olxbr/cross-mlops-model-deploy-action@master](https://github.com/olxbr/cross-mlops-model-deploy-action)

```deploy-steps.yml``` - Realiza o mesmo deploy por passos separados, listados abaixo:

- validação de arquivos de configuração e documentação - [olxbr/cross-mlops-model-doc-validator-action@main](https://github.com/olxbr/cross-mlops-model-doc-validator-action)
- build, containerização e salvamento do serviço - [olxbr/cross-mlops-bentoml-containerize-action@main](https://github.com/olxbr/cross-mlops-bentoml-containerize-action)
- criação de pull-request para repositório central que realizará o deploy - [olxbr/cross-mlops-deploy-request-action@master](https://github.com/olxbr/cross-mlops-deploy-request-action)

## Arquivos para deploy

Para realização do deploy, os arquivos necessários devem estar armazenados na pasta ```model_deploy```. A seguir, podemos ver a lista de arquivos e seus propósitos:

- ```model_save.py``` - Arquivo python que recupera o modelo treinado e o salva na Model Store do BentoML.
- ```service.py``` - Arquivo python que cria o serviço a partir de um modelo salvo na Model Store do BentoML
- ```bentofile.yaml``` - Arquivo de configuração do BentoML necessário para o build e containerização do serviço.
- ```model.yaml``` - Arquivo de documentação do modelo.
- ```deploy.yaml``` - Arquivo de configuração para deploy do modelo no K8s.
- ```requirements.txt``` - Arquivo com as dependências do modelo.


### Exemplo de ```bentofile.yaml```

```yaml
# padrão convencionado para localização do serviço criado do BentoML: <ARQUIVO_SERVICE.py>:<SERVICE_ANNOTATION>
service: "service.py:svc"  
# arquivo de descrição do modelo, no caso, apontando pro arquivo de documentação
description: "file: ./model.yaml" 
# versão do modelo
version: 0.0.1 
docker:
  # imagem docker base para build
  base_image: "bentoml/bento-server:1.0.0a6-python3.9-ubi8-runtime"    
include: 
 # padrão para combinar quais arquivos incluir no bento
 - "*.py"  
python:
  # arquivo de dependencias do modelo
  requirements_txt: "requirements.txt"

```

### Exemplo de ```model.yaml```

Mais informações em: https://olxbr.atlassian.net/l/c/BxEBPRun

```yaml
general:
  product_name: Pipeline Example
  description: Project with svc pipeline example
  status: development
  creation_date: 2022-03-04 18:47:00
  tags: 
ownership:
  bu: cross
  tribe: big data
  squad: dataeng
  repository: https://github.com/olxbr/cross-mlops-model-deploy-example
  developed_by: Michel Arruda
model:
  type: classification
  approach: machine_learning
  retraining: none
  execution_type: real_time
  version: 1.0.0
  last_training_date: 2022-02-18 14:00:00
  dependencies:
business:
  metric: cost
  problem_type: pricing
  use_scope: internal
  critical_level: high
```

### Exemplo de ```deploy.yaml```

```yaml
model_name: pipeline_classifier #nome do modelo
model_version: 1.0.0 #versão do modelo
model_type: real_time #tipo de modelo (real_time, batch)
business_unit: cross #bu (olx, zap, cross)
team: mlops # equipe responsavel pelo modelo
developer: michel # desenvolvedor do modelo
env: preprod #ambiente que o modelo deve ser deployado (prod, preprod, dev)
resources: # (not required) Recursos necessários para o serviço
  requests:
    cpu: 200m
    memory: 2Gi
  limits:
    cpu: 1000m
    memory: 4Gi
```

## Versão das bibliotecas (Pipfile)

- python = 3.8.12
- bentoml = "==1.0.0a6"
- matplotlib = "==3.5.1"# estudos-mlflow
