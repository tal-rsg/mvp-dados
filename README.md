MVP - Disciplina: Sprint: Engenharia de Dados

PUC-RIO

Tales Gonçalves

# Objetivo

Visualizar os dados armazenados na base de dados do aplicativo Strava (www.strava.com) e realizar a carga para o ambiente Azure Data Factory, processar as informações e carrega-las em banco de dados relacional para responder as seguintes questões:

-   Determinar o total de distância percorrida em Kms

-   Maiores médias de batimentos cardíacos e os Kms percorridos

-   Total de distância por tipo de atividade

-   As 10 maiores distâncias percorridas

-   Visualizar os poligonos no mapa usando api do Google Maps

# Detalhamento

Utilizando-se da ferramenta Microsoft Azure Data Factory, será realizado a conexão com a API do Strava para coleta dos dados, processar as informações e carrega-las para um banco de dados relacional para a realização de consultas através de outras ferramentas.

![Esquema de pipeline de dados utilizando API](images/mvp_dados_diagrama_azure-01.png)

## Fonte de Dados

A fonte de dados utiizada são os dados contidos no aplicativo STRAVA, disponíveis através de API, que contempla informações dos usuários, como atividades de ciclismo, corrida entre outros. As informações disponíveis são de uso do próprio usuário registrado no aplicativo e seguindo as suas regras.

A documentação sobre a autenticação pode ser obtida no endereço Strava Developers. (<https://developers.strava.com>)

[![Site Strava Developers](images/site_strava_dev.png)](https://developers.strava.com/)

## Coleta

A extração dos dados é realizada através de conexão com a API do aplicativo, conforme configurado abaixo.

![Conexão API Strava](images/rest_api_azure.png)

Após a conexão os dados são carregados para o inicio do tratamento dentro do data source.

![Dados carregados pela API](images/dtflow_source_data_azure.png)

## Modelagem

Neste exemplo foram utilizados duas etapas de tratamento dos dados, uma selecioando os dados para armazenamento em banco de dados e outra o armazenamento dos polígonos com as coordenadas geográficas do percurso percorrido salvos em arquivo Json.

As informações sobre as atividades foram selecionadas após o recebimento dos dados e preparadas para a carga no banco de dados SQL.

![Select para o banco sql](images/dtflow_select_sql_azure.png)

Recebido os dados é realizado a seleção e o tratamento das informações elas são prepara-las para a carga no banco de dados.

![Seleção dos dados](images/dtflow_select_azure.png)

## Carga

Após a seleção dos dados, realizamos a carga no banco de dados relacional, aqui utilizamos Azure SQL Database, que segue o modelo abaixo:

![](images/sqldatabase_home_azure.png)

Sendo criado um schema no banco de dados com a seguinte tabela: StravaActivities

| Chave |       Nome        |     Tipo     |                                               Descrição                                               |
|:-----------:|:-----------:|:-----------:|:---------------------------------:|
|  PK   |        Id         |   inteiro    |      Código que identifica o registro da atividade. Chave primária. Campo não recebe valor nulo.      |
|       |    start_date     | alfanumérico | Registro da data em que a atividade foi registrada na base de dados. Campo aceita receber valor nulo. |
|       |     distance      |   decimal    |       Registra a quilometragem percorrida durante a atividade. Campo aceita receber valor nulo.       |
|       |    sport_type     | alfanumérico |                Registra o tipo de atividade praticada. Campo aceita receber valor nulo                |
|       |    moving_time    |   inteiro    |          Registra o tempo em movimento durante a atividade. Campo aceita receber valor nulo.          |
|       | average_heartrate |   decimal    |    Registra a média de batimentos cardíacos durante a atividade. Campo aceita receber valor nulo.     |
|       |   average_speed   |   decimal    |           Registra a velocidade média durante a atividade. Campo aceita receber valor nulo.           |

: Schema dbo.StravaActivites

O banco de destino é configurado dentro do fluxo de dados para receber as informações selecionas obedecendo o esquema acima.

![Carga no banco de dados](images/dtflow_sink_azure.png)

No processo de inserção dos dados é verificado o relacionado das colunas

![Mapeamento das colunas](images/dtflow_mapcol_azure.png)

## Análise

[mvp_dados.Rmd](mvp_dados.Rmd)
