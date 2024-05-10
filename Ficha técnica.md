# Ficha técnica

## Análise de dados

### Projeto 2 - Hipóteses

- **Objetivo:**
    
    Auxiliar uma gravadora que deseja lançar um grupo artístico no cenário musical global através da análise de dados do Spotify referente ao ano de 2023, e assim fornecer recomendações estratégicas validando cinco hipóteses levantadas. Estas hipóteses foram desenvolvidas com o objetivo de entender o que faz uma música ser mais ouvida para que assim, a gravadora e o novo grupo musical possam tomar decisões mais concretas que aumentem as chances de alcançar o “sucesso”. 
    
    **As hipóteses levantadas foram:**
    
    - Músicas com BPM (Batidas Por Minuto) mais altos fazem mais sucesso em termos de número de streams no Spotify;
    - As músicas mais populares no ranking do Spotify também possuem um comportamento semelhante em outras plataformas, como a Deezer;
    - A presença de uma música em um maior número de playlists está correlacionada com um maior número de streams;
    - Artistas com um maior número de músicas no Spotify têm mais streams;
    - As características da música influenciam o sucesso em termos de número de streams no Spotify;
- **Equipe:**
    
    Bruna Paiva e Bruna Pereira.
    
- **Ferramentas e Tecnologias:**
    - Big Query, Power BI, Google Colab e GitHub.
    - Linguagem SQL no BigQuery e Python no Power BI e no Google Colab.
- **Processamento e análises:**
    
    O projeto foi desenvolvido em ambientes separados, onde cada participante da equipe realizou as análises em sua máquina. Para as análises feitas nas ferramentas Big Query e no Power Bi, foi optado por utilizar títulos de variavéis iguais durante o projeto para facilitar a compreensão das aálises e dos dados compartilhados entre as participantes . foi utilizado em conjunto o Google Colab e ambas fizeram os testes necessário para alcançar o objetivo final do projeto.
    
    Utilizou-se o Zoom e o Slack como principais meios de comunicação para discutir as possíveis abordagens do projeto. A documentação foi compartilhada, consultas e scripts foram testados em conjunto via Zoom, e as decisões sobre os próximos passos foram tomadas semanalmente.
    
    - Big Query
        
        Informações:
        
        - ID do conjunto de dados: projeto-hipoteses-laboratoria.
        - Nome do conjunto de dados: spotify_2023
        - Nome das tabelas:
            - track_in_competition
            - track_in_spotify
            - track_technical_info
        - Dados nulos
            
            Na tabela na tabela track_in_competition encontramos um total de 50 nulos na coluna in_shazam_charts. Optamos por retirar essa coluna dessa análise por não se tratar de dados de uma stream de música e na verdade ser um serviço de gravação de áudios, além da maioria dos dados disponíveis nessa coluna serem 0.
            
            Na tabela track_in_spotify não encontramos nenhum dado nulo a principio, mas depois de limpar os dados, remover os símbolos e colocar todas as letras em minúsculo encontramos duas músicas com nulos no track_name. Optamos por remover essas músicas também.
            
            Na tabela track_technical_info encontramos 95 nulos na coluna key. Escolhemos retirar a key e mode por serem fora do escopo, além de serem variáveis qualitativas que costumam não apresentar muita precisão na hora de realizar testes.
            
        - Dados duplicados
            
            Encontramos as duplicatas na tabela track_in_spotify e identificamos 4 músicas repetidas dos mesmos artistas. Optamos por não excluir esses dados. Notamos que essas variáveis possuem informações diferentes no track_id, in_spotify_playlists, in_spotify_charts (apenas The Weekend têm informações iguais), streams (apenas Lizzo têm informações iguais) que podem ser utilizadas para análise. Assim como notamos que essas variáveis track_name, artist_s_name, artist_count, released_year, released_mounth e released_day  informam os mesmos valores (apenas Lizzo têm informações diferentes). 
            
        - Dados discrepantes
            
            Identificamos dados discrepantes na variável categórica track_id e na variável númerica streams.  Que foi “0.00”  como id incorreto e no lugar dos números, encontramos todas as informações que seriam de uma linha do track_technical_info no local de streams. Em todos os casos de dados discrepantes, optamos por excluir a linha.
            
        - Dados fora de escopo
            
            As colunas released_year, released_month e released_day foram mudados para released_date para ficar mais organizado os dados criando uma nova variável. A coluna streams que era string foi transformada em integer após remover o dado que apresentava informações de technical_info no lugar dos números de stream da música.
            
        
        Criamos uma tabela auxiliar mostrando a quantidade de música por artista solo que serviu para entender qual é o artista com mais músicas, depois utilizamos esses dados para fazer a coluna amount_of_tracks necessária para criar a coluna  total_streams que serviu para responder a quarta hipótese da gravadora.
        
        Criamos as variáveis released_date para unificar as informações das datas de lançamento que eram divididas em três colunas e a playlists_total para poder realizar os testes estatísticos e, possivelmente, responder a terceira hipótese.
        
        - Criamos os quartis e a segmentação categórica dividida em “Alto” e “Baixo”, pois era necessário para responder a primeira e a quinta hipótese:
            
            bpm_quartil, bpm_categorizada, 
            
            danceability_quartil, danceability_categorizada, 
            
            valence_quartil, valence_categorizada, 
            
            energy_quartil, energy_categorizada, 
            
            acousticness_quartil, acousticness_categorizada,
            
             instrumentalness_quartil, instrumentalness_categorizada, 
            
            liveness_quartil,  liveness_categorizada, 
            
            speechiness_quartil, speechiness_categorizada.
            
        - Queries
            
            Consulta e tratamento dos dados nulos da tabela track_in_competition:
            
            ```sql
            SELECT
            *
            FROM
              `projeto-hipoteses-laboratoria.spotify_2023.track_in_competition`
            WHERE
              track_id IS NULL
              OR in_apple_playlists IS NULL
              OR in_apple_charts IS NULL
              OR in_deezer_playlists IS NULL
              OR in_deezer_charts IS NULL
              OR in_shazam_charts IS NULL;
              
              CREATE OR REPLACE TABLE `projeto-hipoteses-laboratoria.spotify_2023.track_in_competition` AS
            SELECT
              * EXCEPT (in_shazam_charts)
            FROM
              `projeto-hipoteses-laboratoria.spotify_2023.track_in_competition`
            ```
            
            Consulta e tratamento dos dados nulos da tabela track_technical_info:
            
            ```sql
            SELECT
              *
            FROM
              `projeto-hipoteses-laboratoria.spotify_2023.track_technical_info`
            WHERE
              liveness__ IS NULL
              OR speechiness__ IS NULL
              OR instrumentalness__ IS NULL
              OR acousticness__ IS NULL
              OR energy__ IS NULL
              OR valence__ IS NULL
              OR danceability__ IS NULL
              OR mode IS NULL
              OR KEY IS NULL
              OR bpm IS NULL
              OR track_id IS NULL;
              
              CREATE OR REPLACE TABLE `projeto-hipoteses-laboratoria.spotify_2023.track_technical_info` AS
            SELECT
              * EXCEPT (KEY,
                mode)
            FROM
              `projeto-hipoteses-laboratoria.spotify_2023.track_technical_info`
            ```
            
            Consulta de dados duplicados da tabela track_in_spotify:
            
            ```sql
            SELECT
              track_name,
              artist_s__name,
              COUNT(*) AS amount
            FROM
              `projeto-hipoteses-laboratoria.spotify_2023.track_in_spotify`
            GROUP BY
              track_name,
              artist_s__name
            HAVING
              COUNT(*) > 1
            ```
            
            Consulta e tratamento de dados discrepantes:
            
            ```sql
            SELECT
              * EXCEPT (KEY,
                mode)
            FROM
              `projeto-hipoteses-laboratoria.spotify_2023.track_technical_info`
            WHERE 
              NOT REGEXP_CONTAINS(track_id, r'^[0-9]{7}$');
              
              CREATE OR REPLACE TABLE `projeto-hipoteses-laboratoria.spotify_2023.track_technical_info` AS 
            SELECT
              * EXCEPT (KEY,
                mode)
            FROM
              `projeto-hipoteses-laboratoria.spotify_2023.track_technical_info`
            WHERE
              track_id != '0:00'
            AND track_id != '4061483'
            ```
            
            Consulta e tratamento de dados fora de escopo:
            
            ```sql
            SELECT
                track_id,
                LOWER(REGEXP_REPLACE(S.track_name, r'[^\w]+', ' ')) AS track_name_cleaned,
                LOWER(REGEXP_REPLACE(S.artist_s__name, r'[^\w]+', ' ')) AS artists_name_cleaned,
                DATE(CONCAT(released_year, '-', released_month, '-', released_day)) AS released_date,
                in_spotify_charts,
                in_spotify_playlists,
                artist_count,
                CAST(S.streams AS INT64) AS streams_clean
            FROM 
                `projeto-hipoteses-laboratoria.spotify_2023..track_in_spotify` 
               
             
            ```
            
            Criação de novas variáveis:
            
            ```sql
            CREATE OR REPLACE TABLE projeto-hipoteses-laboratoria.spotify_2023.analysis_spotify AS
            SELECT
              *,
             DATE(CONCAT(released_year, '-', released_month, '-', released_day)) AS released_date,
              in_spotify_playlists + in_apple_playlists + in_deezer_playlists AS playlists_total
            FROM
              projeto-hipoteses-laboratoria.spotify_2023.analysis_spotify;
            ```
            
            Criação das variáveis amount_of_tracks e total_streams:
            
            ```sql
            CREATE OR REPLACE TABLE `projeto-hipoteses-laboratoria.spotify_2023.amount_tracks_solo_` AS
            SELECT amount_tracks_solo_artist.artists_name_clean,
                   amount_tracks_solo_artist.amount_of_tracks,
                   subquery.total_streams
            FROM projeto-hipoteses-laboratoria.spotify_2023.amount_tracks_solo_artist
            JOIN (
                SELECT analysis_spotify.artists_name_clean,
                       SUM(analysis_spotify.streams_clean) AS total_streams
                FROM projeto-hipoteses-laboratoria.spotify_2023.amount_tracks_solo_artist
                JOIN projeto-hipoteses-laboratoria.spotify_2023.analysis_spotify 
                    ON amount_tracks_solo_artist.artists_name_clean = analysis_spotify.artists_name_clean
                GROUP BY analysis_spotify.artists_name_clean
            ) AS subquery
            ON amount_tracks_solo_artist.artists_name_clean = subquery.artists_name_clean;
            
            ```
            
            Criação de quartil e da segmentação categórica:
            
            ```sql
            CREATE OR REPLACE TABLE `spotify_2023.analysis_spotify` AS
            WITH quartis AS (
              SELECT
                track_id,
                bpm,
                NTILE(4) OVER (ORDER BY bpm) AS bpm_quartil,
                danceability__,
                NTILE(4) OVER (ORDER BY danceability__) AS danceability_quartil,
                valence__,
                NTILE(4) OVER (ORDER BY valence__) AS valence_quartil,
                energy__,
                NTILE(4) OVER (ORDER BY energy__) AS energy_quartil,
                acousticness__,
                NTILE(4) OVER (ORDER BY acousticness__) AS acousticness_quartil,
                instrumentalness__,
                NTILE(4) OVER (ORDER BY instrumentalness__) AS instrumentalness_quartil,
                liveness__,
                NTILE(4) OVER (ORDER BY liveness__) AS liveness_quartil,
                speechiness__,
                NTILE(4) OVER (ORDER BY speechiness__) AS speechiness_quartil
              FROM
                `spotify_2023.analysis_spotify`
            )
            SELECT
              a.*,
              q.bpm_quartil,
              IF(q.bpm_quartil=4, "Alto", "Baixo") AS bpm_categorizada,
              q.danceability_quartil,
              IF (q.danceability_quartil=4, "Alto", "Baixo") AS danceability_categorizada,
              q.valence_quartil,
              IF (valence_quartil=4, "Alto", "Baixo") AS valence_categorizada,
              q.energy_quartil,
              IF (energy_quartil=4, "Alto", "Baixo") AS energy_categorizada,
              q.acousticness_quartil,
              IF (acousticness_quartil=4, "Alto", "Baixo") AS acousticness_categorizada,
              q.instrumentalness_quartil,
              IF (instrumentalness_quartil=4, "Alto", "Baixo") AS instrumentalness_categorizada,
              q.liveness_quartil,
              IF (liveness_quartil=4, "Alto", "Baixo") AS liveness_categorizada,
              q.speechiness_quartil,
              IF (speechiness_quartil=4, "Alto", "Baixo") AS speechiness_categorizada
            FROM 
              `spotify_2023.analysis_spotify` a
            JOIN 
              quartis q ON a.track_id = q.track_id
            ```
            
    - Power BI
        
        Utilizamos scorecards para informar os diversos dados da base como nome da música, nome do artista, quantas playlists, quantos streams, mês e ano de lançamento da música, colocação nos rankings do Spotify, Deezer e Apple, além das informações de características tecnicas como BPM, dançabilidade, energia, instrumentalidade, vivacidade, fala, valência e acústica.
        
        Escolhemos gráficos de barra para listar os top 10 das seguintes categorias: artistas com mais músicas, artistas com mais streams e músicas com mais streams.
        
        Fizemos um tabela matricial para visualizar informações sobre as músicas, colocando o nome das músicas, a quantidade de streams, a data de lançamento, a quantidade de playlists e a posição no ranking do Spotify.
        
        Para ter uma visualização dos anos de lançamentos das músicas e dos streams por ano de lançamento utilizamos gráficos de linhas.
        
        Nas medidas de tendência central e medidas de dispersão fizemos uma tabela matricial com os valores de média, mediana, soma, desvio padrão e variância das playlists, streams e BPM.
        
        Observamos as média de stream por categorização “Alto” e “Baixo” das características de música.
        
        Também fizemos a correlação de Pearson para começar a obter as respostas sobre as hipóteses levantadas no objetivo da análise, sendo um bom teste estatístico para saber se o número de streams está correlacionado ao número total de playlists ou se artistas com mais músicas tem correlação com o total de streams.
        
        - Script
            
            Código do histograma de Playlist utilizado no Power BI:
            
            ```sql
            # O código a seguir para criar um dataframe e remover as linhas duplicadas sempre é executado e age como um preâmbulo para o script: 
            
            # dataset = pandas.DataFrame(playlist_total)
            # dataset = dataset.drop_duplicates()
            
            # Cole ou digite aqui seu código de script:
            import matplotlib.pyplot as plt
            import pandas as pd
            
            # Criar um DataFrame com os dados fornecidos
            data = dataset [['playlist_total']]
            
            # Definindo as cores de fundo e de texto
            plt.rcParams['figure.facecolor'] = 'black'
            plt.rcParams['axes.facecolor'] = 'black'
            plt.rcParams['axes.edgecolor'] = 'black'
            plt.rcParams['axes.labelcolor'] = 'black'
            plt.rcParams['xtick.color'] = 'black'
            plt.rcParams['ytick.color'] = 'black'
            plt.rcParams['axes.edgecolor'] = 'green'
            
            # Criar o histograma
            plt.hist(data, bins=10, color='lightgreen', alpha=0.6)
            plt.xlabel('Valor', fontsize=20)
            plt.ylabel('Frequência', fontsize=20)
            plt.title('Total de playlists por música', fontsize=23)
            
            # Exibir o histograma
            plt.show()
            ```
            
            Código do histograma de Streams utilizado no Power BI:
            
            ```sql
            data = dataset [['streams_clean']]
            
            # Definindo as cores de fundo e de texto
            plt.rcParams['figure.facecolor'] = 'black'
            plt.rcParams['axes.facecolor'] = 'black'
            plt.rcParams['axes.edgecolor'] = 'black'
            plt.rcParams['axes.labelcolor'] = 'black'
            plt.rcParams['xtick.color'] = 'black'
            plt.rcParams['ytick.color'] = 'black'
            plt.rcParams['axes.edgecolor'] = 'green'
            
            # Criar o histograma
            plt.hist(data, bins=10, color='lightgreen', alpha=0.6)
            plt.xlabel('Valor', fontsize=20)
            plt.ylabel('Frequência', fontsize=20)
            plt.title('Total de streams', fontsize=23)
            
            # Exibir o histograma
            plt.show()
            ```
            
        
    - Google Colab
        
        Fizemos diferentes testes estatísticos para obter as respostas das hipóteses que apresentam variáveis de diferentes tipos. Nas hipóteses I, III e IV utilizamos os testes de Correlação de Pearson e Regressão linear; na hipótese II utilizamos os testes Correlação de Spearman e Regressão linear e na hipótese V fizemos os testes de Shapiro, Mann-Whitney U, Correlação de Spearman e Regressão linear.
        
        Para visualizar os dados obtidos com esses testes nos utilizamos gráficos de dispersão, histogramas e tabelas resumo.
        
        - Script
            
            Bibliotecas e base dados:
            
            ```python
            import pandas as pd
            import numpy as np
            import matplotlib.pyplot as plt
            from sklearn.linear_model import LinearRegression
            from sklearn.model_selection import train_test_split
            from sklearn.metrics import r2_score
            import statsmodels.api as sm
            from scipy.stats import mannwhitneyu
            from scipy.stats import spearmanr
            from scipy.stats import shapiro
            from scipy.stats import pearsonr
            from scipy import stats
            import seaborn as sns
            
            df = pd.read_csv('spotify.csv')
            ```
            
            Hipótese I:
            
            ```python
            data_bpm_high = df[df['bpm'] > 140]
            
            bpm = data_bpm_high['bpm'].values
            streams_clean = data_bpm_high['streams_clean'].values
            
            model = LinearRegression()
            model.fit(bpm.reshape(-1, 1), streams_clean)
            
            r, p_value = pearsonr(bpm, streams_clean)
            r_rounded = round(r, 3)
            p_value_rounded = round(p_value, 3)
            resultados_adicionais = {
                'Teste': ['Regressão linear entre "BPM Alto" e "Streams"'],
                'Coeficiente de correlação': [r],
                'Coeficiente de correlação (Arredondado)': [r_rounded],
                'p-valor': [p_value],
                'p-valor (Arredondado)': [p_value_rounded]
            }
            
            df_resultados_adicionais = pd.DataFrame(resultados_adicionais)
            
            display(df_resultados_adicionais)
            
            plt.scatter(bpm, streams_clean, color='#634161', label='Músicas analisadas')
            
            bpm_range = np.linspace(bpm.min(), bpm.max(), 100)
            predicted_streams = model.predict(bpm_range.reshape(-1, 1))
            plt.plot(bpm_range, predicted_streams, color='#FFA500', label='Regressão linear')
            
            plt.title('Correlação entre BPM Alto e quantidade de streams')
            plt.legend()
            plt.show()
            ```
            
            Hipótese II:
            
            ```python
            corr_spotify_apple, p_value_spotify_apple = spearmanr(df['in_spotify_charts'], df['in_apple_charts'], nan_policy='omit')
            
            corr_spotify_deezer, p_value_spotify_deezer = spearmanr(df['in_spotify_charts'], df['in_deezer_charts'], nan_policy='omit')
            
            corr_spotify_apple_rounded = round(corr_spotify_apple, 3)
            p_value_spotify_apple_rounded = round(p_value_spotify_apple, 3)
            corr_spotify_deezer_rounded = round(corr_spotify_deezer, 3)
            p_value_spotify_deezer_rounded = round(p_value_spotify_deezer, 3)
            
            resultados_adicionais = {
                'Teste': ['Correlação de Spearman Spotify - Apple', 'Correlação de Spearman Spotify - Deezer'],
                'Coeficiente de Correlação': [corr_spotify_apple, corr_spotify_deezer],
                'Coeficiente de Correlação (Arredondado)': [corr_spotify_apple_rounded, corr_spotify_deezer_rounded],
                'p-valor': [p_value_spotify_apple, p_value_spotify_deezer],
                'p-valor (Arredondado)': [p_value_spotify_apple_rounded, p_value_spotify_deezer_rounded]
            }
            
            df_resultados_adicionais = pd.DataFrame(resultados_adicionais)
            
            display(df_resultados_adicionais)
            
            fig, axes = plt.subplots(1, 2, figsize=(15, 6))
            
            regressor = LinearRegression()
            regressor.fit(df[['in_spotify_charts']], df['in_apple_charts'])
            y_apple_pred = regressor.predict(df[['in_spotify_charts']])
            
            regressor_deezer = LinearRegression()
            regressor_deezer.fit(df[['in_spotify_charts']], df['in_deezer_charts'])
            y_deezer_pred = regressor_deezer.predict(df[['in_spotify_charts']])
            
            bubble_size = 100
            sns.scatterplot(x=df['in_spotify_charts'], y=df['in_apple_charts'], ax=axes[0], color='#634161', label='Músicas analisadas', edgecolor='w', s=bubble_size)
            axes[0].plot(df['in_spotify_charts'], y_apple_pred, color='#FFA500', label='Regressão linear', linewidth=2)
            axes[0].set_title('Comportamento dos rankings do Spotify e Apple Music')
            axes[0].set_xlabel('Ranking Spotify')
            axes[0].set_ylabel('Ranking Apple')
            axes[0].legend()
            
            bubble_size = 100
            sns.scatterplot(x=df['in_spotify_charts'], y=df['in_deezer_charts'], ax=axes[1], color='#634161', label='Músicas analisadas', edgecolor='w', s=bubble_size)
            axes[1].plot(df['in_spotify_charts'], y_deezer_pred, color='#FFA500', label='Regressão linear', linewidth=2)
            axes[1].set_title('Comportamento dos rankings do Spotify e Deezer')
            axes[1].set_xlabel('Ranking Spotify')
            axes[1].set_ylabel('Ranking Deezer')
            axes[1].legend()
            
            plt.tight_layout()
            plt.show()
            ```
            
            Hipóteses III:
            
            ```python
            playlists_total = df['playlists_total'].values
            streams_clean = df['streams_clean'].values
            
            r2, p_value2 = pearsonr(playlists_total, streams_clean)
            r2_rounded = round(r2, 3)
            p_value2_rounded = round(p_value2, 3)
            r1, p_value1 = pearsonr(playlists_total, streams_clean)
            resultados_adicionais = {
                'Teste': ['Correlação de Pearson'],
                'Coeficiente de Correlação': [r2],
                'Coeficiente de Correlação (Arredondado)': [r2_rounded],
                'p-valor': [p_value2],
                'p-valor (Arredondado)': [p_value2_rounded]
            }
            display(df_resultados_adicionais)
            df_resultados_adicionais = pd.DataFrame(resultados_adicionais)
            
            plt.scatter(playlists_total, streams_clean, color='#634161', label='Músicas analisadas')
            
            model1 = LinearRegression()
            model1.fit(playlists_total.reshape(-1, 1), streams_clean)
            predicted_spotify_charts = model1.predict(playlists_total.reshape(-1, 1))
            plt.plot(playlists_total, predicted_spotify_charts, color='#FFA500', label='Regressão linear')
            
            plt.title('Correlação entre o número de Playlists e o número de Streams')
            plt.legend()
            plt.show()
            ```
            
            Hipóteses IV:
            
            ```python
            amount_of_tracks = df['amount_of_tracks'].values
            total_streams = df['total_streams'].values
            
            amount_of_tracks_clean = amount_of_tracks[~np.isnan(amount_of_tracks)]
            total_streams_clean = total_streams[~np.isnan(total_streams)]
            r1, p_value1 = pearsonr(amount_of_tracks_clean, total_streams_clean)
            
            r1_rounded = round(r1, 2)
            p_value1_rounded = round(p_value1, 2)
            
            resultados_resumo = {
                'Teste': ['Correlação de Pearson'],
                'Coeficiente de correlação': [r1],
                'Coeficiente de correlação (Arredondado)': [r1_rounded],
                'p-valor': [p_value1],
                'p-valor (Arredondado)': [p_value1_rounded]
            }
            
            df_resultados_resumo = pd.DataFrame(resultados_resumo)
            
            display(df_resultados_resumo)
            
            plt.scatter(amount_of_tracks_clean, total_streams_clean, color='#634161', label='Músicas analisadas')
            
            model1 = LinearRegression()
            model1.fit(amount_of_tracks_clean.reshape(-1, 1), total_streams_clean)
            predicted_total_streams = model1.predict(amount_of_tracks_clean.reshape(-1, 1))
            plt.plot(amount_of_tracks_clean, predicted_total_streams, color='#FFA500', label='Regressão linear')
            
            plt.title('Correlação entre número de músicas por artista e o total de streams por artista')
            plt.legend()
            plt.show()
            ```
            
            Hipóteses V - BPM:
            
            ```python
            shapiro_bpm, p_value_bpm = shapiro(df['bpm'])
            shapiro_streams, p_value_streams = shapiro(df['streams_clean'])
            
            statistic_mwu, p_value_mwu = mannwhitneyu(df['bpm'], df['streams_clean'])
            
            spearman_corr, p_value_spearman = spearmanr(df['bpm'], df['streams_clean'])
            
            model = LinearRegression()
            model.fit(df['bpm'].values.reshape(-1, 1), df['streams_clean'])
            coeficiente_angular = model.coef_[0]
            intercepto_linear = model.intercept_
            
            fig, axs = plt.subplots(1, 2, figsize=(15, 5))
            
            axs[0].scatter(df['bpm'], df['streams_clean'], color='#634161', label='Músicas analisadas')
            axs[0].plot(df['bpm'], intercepto_linear + coeficiente_angular * df['bpm'], color='#FFA500', label='Regressão linear')
            axs[0].set_title('Correlação entre as Batidas Por Minuto e o total de Streams')
            axs[0].set_xlabel('Batidas Por Minuto')
            axs[0].set_ylabel('Streams')
            axs[0].legend()
            
            axs[1].hist(df['bpm'], bins=20, color='#634161', edgecolor='white')
            axs[1].set_title('Histograma das Batidas Por Minuto')
            
            plt.tight_layout()
            plt.show()
            
            shapiro_bpm_rounded = round(shapiro_bpm, 2)
            shapiro_streams_rounded = round(shapiro_streams, 2)
            statistic_mwu_rounded = round(statistic_mwu, 2)
            spearman_corr_rounded = round(spearman_corr, 2)
            coeficiente_angular_rounded = round(coeficiente_angular, 2)
            intercepto_linear_rounded = round(intercepto_linear, 2)
            
            resultados = {
                'Testes': ['Shapiro de Batidas Por Minuto', 'Shapiro de Streams', 'Mann-Whitney U', 'Correlação de Spearman'],
                'Estatística/Coeficiente': [shapiro_bpm, shapiro_streams, statistic_mwu, spearman_corr],
                'Estatística/Coeficiente (Arredondado)': [shapiro_bpm_rounded, shapiro_streams_rounded, statistic_mwu_rounded, spearman_corr_rounded],
                'p-valor/Intercepto Linear': [p_value_bpm, p_value_streams, p_value_mwu, p_value_spearman],
                'p-valor/Intercepto Linear (Arredondado)': [f'{p_value_bpm:.2f}', f'{p_value_streams:.2f}', f'{p_value_mwu:.2f}', f'{p_value_spearman:.2f}']
            }
            
            df_resultados = pd.DataFrame(resultados)
            
            display(df_resultados)
            ```
            
            Hipóteses V - Danceability: 
            
            ```python
            shapiro_danceability__, p_value_danceability__ = shapiro(df['danceability__'])
            shapiro_streams, p_value_streams = shapiro(df['streams_clean'])
            
            statistic_mwu, p_value_mwu = mannwhitneyu(df['danceability__'], df['streams_clean'])
            
            spearman_corr, p_value_spearman = spearmanr(df['danceability__'], df['streams_clean'])
            
            model = LinearRegression()
            model.fit(df['danceability__'].values.reshape(-1, 1), df['streams_clean'])
            coeficiente_angular = model.coef_[0]
            intercepto_linear = model.intercept_
            
            fig, axs = plt.subplots(1, 2, figsize=(15, 5))
            
            axs[0].scatter(df['danceability__'], df['streams_clean'], color='#634161', label='Músicas analisadas')
            axs[0].plot(df['danceability__'], intercepto_linear + coeficiente_angular * df['danceability__'], color='#FFA500', label='Regressão linear')
            axs[0].set_title('Correlação entre as danceabilidade das músicas e o total de Streams')
            axs[0].set_xlabel('Danceabilidade')
            axs[0].set_ylabel('Streams')
            axs[0].legend()
            
            axs[1].hist(df['danceability__'], bins=20, color='#634161', edgecolor='white')
            axs[1].set_title('Histograma das danceabilidade das músicas')
            
            plt.tight_layout()
            plt.show()
            
            shapiro_bpm_rounded = round(shapiro_bpm, 2)
            shapiro_streams_rounded = round(shapiro_streams, 2)
            statistic_mwu_rounded = round(statistic_mwu, 2)
            spearman_corr_rounded = round(spearman_corr, 2)
            coeficiente_angular_rounded = round(coeficiente_angular, 2)
            intercepto_linear_rounded = round(intercepto_linear, 2)
            
            resultados = {
                'Testes': ['Shapiro de Danceabilidade', 'Shapiro de Streams', 'Mann-Whitney U', 'Correlação de Spearman'],
                'Estatística/Coeficiente': [shapiro_bpm, shapiro_streams, statistic_mwu, spearman_corr],
                'Estatística/Coeficiente (Arredondado)': [shapiro_bpm_rounded, shapiro_streams_rounded, statistic_mwu_rounded, spearman_corr_rounded],
                'p-valor/Intercepto Linear': [p_value_bpm, p_value_streams, p_value_mwu, p_value_spearman],
                'p-valor/Intercepto Linear (Arredondado)': [f'{p_value_bpm:.2f}', f'{p_value_streams:.2f}', f'{p_value_mwu:.2f}', f'{p_value_spearman:.2f}']
            }
            
            df_resultados = pd.DataFrame(resultados)
            
            display(df_resultados)
            ```
            
            Hipóteses V - Acousticness:
            
            ```python
            shapiro_acousticness__, p_value_acousticness__ = shapiro(df['acousticness__'])
            shapiro_streams, p_value_streams = shapiro(df['streams_clean'])
            
            statistic_mwu, p_value_mwu = mannwhitneyu(df['acousticness__'], df['streams_clean'])
            
            spearman_corr, p_value_spearman = spearmanr(df['acousticness__'], df['streams_clean'])
            
            model = LinearRegression()
            model.fit(df['acousticness__'].values.reshape(-1, 1), df['streams_clean'])
            coeficiente_angular = model.coef_[0]
            intercepto_linear = model.intercept_
            
            fig, axs = plt.subplots(1, 2, figsize=(15, 5))
            
            axs[0].scatter(df['acousticness__'], df['streams_clean'], color='#634161', label='Músicas analisadas')
            axs[0].plot(df['acousticness__'], intercepto_linear + coeficiente_angular * df['acousticness__'], color='#FFA500', label='Regressão linear')
            axs[0].set_title('Correlação entre a acústica das músicas e o total de Streams')
            axs[0].set_xlabel('Acústica')
            axs[0].set_ylabel('Streams')
            axs[0].legend()
            
            axs[1].hist(df['acousticness__'], bins=20, color='#634161', edgecolor='white')
            axs[1].set_title('Histograma da acústica das músicas')
            
            plt.tight_layout()
            plt.show()
            
            shapiro_acousticness___rounded = round(shapiro_acousticness__, 2)
            shapiro_streams_rounded = round(shapiro_streams, 2)
            statistic_mwu_rounded = round(statistic_mwu, 2)
            spearman_corr_rounded = round(spearman_corr, 2)
            coeficiente_angular_rounded = round(coeficiente_angular, 2)
            intercepto_linear_rounded = round(intercepto_linear, 2)
            
            resultados = {
                'Testes': ['Shapiro de Acústica', 'Shapiro de Streams', 'Mann-Whitney U', 'Correlação de Spearman'],
                'Estatística/Coeficiente': [shapiro_acousticness__, shapiro_streams, statistic_mwu, spearman_corr],
                'Estatística/Coeficiente (Arredondado)': [shapiro_acousticness___rounded, shapiro_streams_rounded, statistic_mwu_rounded, spearman_corr_rounded],
                'p-valor/Intercepto Linear': [p_value_acousticness__, p_value_streams, p_value_mwu, p_value_spearman],
                'p-valor/Intercepto Linear (Arredondado)': [f'{p_value_acousticness__:.2f}', f'{p_value_streams:.2f}', f'{p_value_mwu:.2f}', f'{p_value_spearman:.2f}']
            }
            
            df_resultados = pd.DataFrame(resultados)
            
            display(df_resultados)
            ```
            
            Hipóteses V - Energy:
            
            ```python
            shapiro_energy__, p_value_energy__ = shapiro(df['energy__'])
            shapiro_streams, p_value_streams = shapiro(df['streams_clean'])
            
            statistic_mwu, p_value_mwu = mannwhitneyu(df['energy__'], df['streams_clean'])
            
            spearman_corr, p_value_spearman = spearmanr(df['energy__'], df['streams_clean'])
            
            model = LinearRegression()
            model.fit(df['energy__'].values.reshape(-1, 1), df['streams_clean'])
            coeficiente_angular = model.coef_[0]
            intercepto_linear = model.intercept_
            
            fig, axs = plt.subplots(1, 2, figsize=(15, 5))
            
            axs[0].scatter(df['energy__'], df['streams_clean'], color='#634161', label='Músicas analisadas')
            axs[0].plot(df['energy__'], intercepto_linear + coeficiente_angular * df['energy__'], color='#FFA500', label='Regressão linear')
            axs[0].set_title('Correlação entre a energia das músicas e o total de Streams')
            axs[0].set_xlabel('Energia')
            axs[0].set_ylabel('Streams')
            axs[0].legend()
            
            axs[1].hist(df['energy__'], bins=20, color='#634161', edgecolor='white')
            axs[1].set_title('Histograma da energia das músicas')
            
            plt.tight_layout()
            plt.show()
            
            shapiro_energy___rounded = round(shapiro_energy__, 2)
            shapiro_streams_rounded = round(shapiro_streams, 2)
            statistic_mwu_rounded = round(statistic_mwu, 2)
            spearman_corr_rounded = round(spearman_corr, 2)
            coeficiente_angular_rounded = round(coeficiente_angular, 2)
            intercepto_linear_rounded = round(intercepto_linear, 2)
            
            resultados = {
                'Testes': ['Shapiro de Energia', 'Shapiro de Streams', 'Mann-Whitney U', 'Correlação de Spearman'],
                'Estatística/Coeficiente': [shapiro_energy__, shapiro_streams, statistic_mwu, spearman_corr],
                'Estatística/Coeficiente (Arredondado)': [shapiro_energy___rounded, shapiro_streams_rounded, statistic_mwu_rounded, spearman_corr_rounded],
                'p-valor/Intercepto Linear': [p_value_energy__, p_value_streams, p_value_mwu, p_value_spearman],
                'p-valor/Intercepto Linear (Arredondado)': [f'{p_value_energy__:.2f}', f'{p_value_streams:.2f}', f'{p_value_mwu:.2f}', f'{p_value_spearman:.2f}']
            }
            
            df_resultados = pd.DataFrame(resultados)
            
            display(df_resultados)
            ```
            
            Hipóteses V - Instrumentalness:
            
            ```python
            shapiro_instrumentalness__, p_value_instrumentalness__ = shapiro(df['instrumentalness__'])
            shapiro_streams, p_value_streams = shapiro(df['streams_clean'])
            
            statistic_mwu, p_value_mwu = mannwhitneyu(df['instrumentalness__'], df['streams_clean'])
            
            spearman_corr, p_value_spearman = spearmanr(df['instrumentalness__'], df['streams_clean'])
            
            model = LinearRegression()
            model.fit(df['instrumentalness__'].values.reshape(-1, 1), df['streams_clean'])
            coeficiente_angular = model.coef_[0]
            intercepto_linear = model.intercept_
            
            fig, axs = plt.subplots(1, 2, figsize=(15, 5))
            
            axs[0].scatter(df['instrumentalness__'], df['streams_clean'], color='#634161', label='Músicas analisadas')
            axs[0].plot(df['instrumentalness__'], intercepto_linear + coeficiente_angular * df['instrumentalness__'], color='#FFA500', label='Regressão linear')
            axs[0].set_title('Correlação entre instrumental das músicas e o total de Streams')
            axs[0].set_xlabel('Instrumental')
            axs[0].set_ylabel('Streams')
            axs[0].legend()
            
            axs[1].hist(df['instrumentalness__'], bins=20, color='#634161', edgecolor='white')
            axs[1].set_title('Histograma do Instrumental das músicas')
            
            plt.tight_layout()
            plt.show()
            
            shapiro_instrumentalness___rounded = round(shapiro_instrumentalness__, 2)
            shapiro_streams_rounded = round(shapiro_streams, 2)
            statistic_mwu_rounded = round(statistic_mwu, 2)
            spearman_corr_rounded = round(spearman_corr, 2)
            coeficiente_angular_rounded = round(coeficiente_angular, 2)
            intercepto_linear_rounded = round(intercepto_linear, 2)
            
            resultados = {
                'Testes': ['Shapiro do Instrumental', 'Shapiro de Streams', 'Mann-Whitney U', 'Correlação de Spearman'],
                'Estatística/Coeficiente': [shapiro_instrumentalness__, shapiro_streams, statistic_mwu, spearman_corr],
                'Estatística/Coeficiente (Arredondado)': [shapiro_instrumentalness___rounded, shapiro_streams_rounded, statistic_mwu_rounded, spearman_corr_rounded],
                'p-valor/Intercepto Linear': [p_value_instrumentalness__, p_value_streams, p_value_mwu, p_value_spearman],
                'p-valor/Intercepto Linear (Arredondado)': [f'{p_value_instrumentalness__:.2f}', f'{p_value_streams:.2f}', f'{p_value_mwu:.2f}', f'{p_value_spearman:.2f}']
            }
            
            df_resultados = pd.DataFrame(resultados)
            
            display(df_resultados)
            ```
            
            Hipóteses V - Speechiness:
            
            ```python
            shapiro_speechiness__, p_value_speechiness__ = shapiro(df['speechiness__'])
            shapiro_streams, p_value_streams = shapiro(df['streams_clean'])
            
            statistic_mwu, p_value_mwu = mannwhitneyu(df['speechiness__'], df['streams_clean'])
            
            spearman_corr, p_value_spearman = spearmanr(df['speechiness__'], df['streams_clean'])
            
            model = LinearRegression()
            model.fit(df['speechiness__'].values.reshape(-1, 1), df['streams_clean'])
            coeficiente_angular = model.coef_[0]
            intercepto_linear = model.intercept_
            
            fig, axs = plt.subplots(1, 2, figsize=(15, 5))
            
            axs[0].scatter(df['speechiness__'], df['streams_clean'], color='#634161', label='Músicas analisadas')
            axs[0].plot(df['speechiness__'], intercepto_linear + coeficiente_angular * df['speechiness__'], color='#FFA500', label='Regressão linear')
            axs[0].set_title('Correlação entre a quantidade de palavras cantadas pelos artistas e o total de Streams')
            axs[0].set_xlabel('Palavras')
            axs[0].set_ylabel('Streams')
            axs[0].legend()
            
            axs[1].hist(df['speechiness__'], bins=20, color='#634161', edgecolor='white')
            axs[1].set_title('Histograma da quantidade de palavras cantadas pelos artistas')
            
            plt.tight_layout()
            plt.show()
            
            shapiro_speechiness___rounded = round(shapiro_speechiness__, 2)
            shapiro_streams_rounded = round(shapiro_streams, 2)
            statistic_mwu_rounded = round(statistic_mwu, 2)
            spearman_corr_rounded = round(spearman_corr, 2)
            coeficiente_angular_rounded = round(coeficiente_angular, 2)
            intercepto_linear_rounded = round(intercepto_linear, 2)
            
            resultados = {
                'Testes': ['Shapiro das Palavras', 'Shapiro de Streams', 'Mann-Whitney U', 'Correlação de Spearman'],
                'Estatística/Coeficiente': [shapiro_speechiness__, shapiro_streams, statistic_mwu, spearman_corr],
                'Estatística/Coeficiente (Arredondado)': [shapiro_speechiness___rounded, shapiro_streams_rounded, statistic_mwu_rounded, spearman_corr_rounded],
                'p-valor/Intercepto Linear': [p_value_speechiness__, p_value_streams, p_value_mwu, p_value_spearman],
                'p-valor/Intercepto Linear (Arredondado)': [f'{p_value_speechiness__:.2f}', f'{p_value_streams:.2f}', f'{p_value_mwu:.2f}', f'{p_value_spearman:.2f}']
            }
            
            df_resultados = pd.DataFrame(resultados)
            
            display(df_resultados)
            ```
            
            Hipóteses V - Liveness:
            
            ```python
            shapiro_liveness__, p_value_liveness__ = shapiro(df['liveness__'])
            shapiro_streams, p_value_streams = shapiro(df['streams_clean'])
            
            statistic_mwu, p_value_mwu = mannwhitneyu(df['liveness__'], df['streams_clean'])
            
            spearman_corr, p_value_spearman = spearmanr(df['liveness__'], df['streams_clean'])
            
            model = LinearRegression()
            model.fit(df['liveness__'].values.reshape(-1, 1), df['streams_clean'])
            coeficiente_angular = model.coef_[0]
            intercepto_linear = model.intercept_
            
            fig, axs = plt.subplots(1, 2, figsize=(15, 5))
            
            axs[0].scatter(df['liveness__'], df['streams_clean'], color='#634161', label='Músicas analisadas')
            axs[0].plot(df['liveness__'], intercepto_linear + coeficiente_angular * df['liveness__'], color='#FFA500', label='Regressão linear')
            axs[0].set_title('Correlação entre a vivacidade das músicas e o total de Streams')
            axs[0].set_xlabel('Vivacidade')
            axs[0].set_ylabel('Streams')
            axs[0].legend()
            
            axs[1].hist(df['liveness__'], bins=20, color='#634161', edgecolor='white')
            axs[1].set_title('Histograma da vivacidade das músicas')
            
            plt.tight_layout()
            plt.show()
            
            shapiro_liveness___rounded = round(shapiro_liveness__, 2)
            shapiro_streams_rounded = round(shapiro_streams, 2)
            statistic_mwu_rounded = round(statistic_mwu, 2)
            spearman_corr_rounded = round(spearman_corr, 2)
            coeficiente_angular_rounded = round(coeficiente_angular, 2)
            intercepto_linear_rounded = round(intercepto_linear, 2)
            
            resultados = {
                'Testes': ['Shapiro da Vivacidade', 'Shapiro de Streams', 'Mann-Whitney U', 'Correlação de Spearman'],
                'Estatística/Coeficiente': [shapiro_liveness__, shapiro_streams, statistic_mwu, spearman_corr],
                'Estatística/Coeficiente (Arredondado)': [shapiro_liveness___rounded, shapiro_streams_rounded, statistic_mwu_rounded, spearman_corr_rounded],
                'p-valor/Intercepto Linear': [p_value_liveness__, p_value_streams, p_value_mwu, p_value_spearman],
                'p-valor/Intercepto Linear (Arredondado)': [f'{p_value_liveness__:.2f}', f'{p_value_streams:.2f}', f'{p_value_mwu:.2f}', f'{p_value_spearman:.2f}']
            }
            
            df_resultados = pd.DataFrame(resultados)
            
            display(df_resultados)
            ```
            
            Hipóteses V - Valence:
            
            ```python
            shapiro_valence__, p_value_valence__ = shapiro(df['valence__'])
            shapiro_streams, p_value_streams = shapiro(df['streams_clean'])
            
            statistic_mwu, p_value_mwu = mannwhitneyu(df['valence__'], df['streams_clean'])
            
            spearman_corr, p_value_spearman = spearmanr(df['valence__'], df['streams_clean'])
            
            model = LinearRegression()
            model.fit(df['valence__'].values.reshape(-1, 1), df['streams_clean'])
            coeficiente_angular = model.coef_[0]
            intercepto_linear = model.intercept_
            
            fig, axs = plt.subplots(1, 2, figsize=(15, 5))
            
            axs[0].scatter(df['valence__'], df['streams_clean'], color='#634161', label='Músicas analisadas')
            axs[0].plot(df['valence__'], intercepto_linear + coeficiente_angular * df['valence__'], color='#FFA500', label='Regressão linear')
            axs[0].set_title('Correlação entre a valência das músicas e o total de Streams')
            axs[0].set_xlabel('Valência')
            axs[0].set_ylabel('Streams')
            axs[0].legend()
            
            axs[1].hist(df['valence__'], bins=20, color='#634161', edgecolor='white')
            axs[1].set_title('Histograma da valência das músicas')
            
            plt.tight_layout()
            plt.show()
            
            shapiro_valence___rounded = round(shapiro_valence__, 2)
            shapiro_streams_rounded = round(shapiro_streams, 2)
            statistic_mwu_rounded = round(statistic_mwu, 2)
            spearman_corr_rounded = round(spearman_corr, 2)
            coeficiente_angular_rounded = round(coeficiente_angular, 2)
            intercepto_linear_rounded = round(intercepto_linear, 2)
            
            resultados = {
                'Testes': ['Shapiro da Valência', 'Shapiro de Streams', 'Mann-Whitney U', 'Correlação de Spearman'],
                'Estatística/Coeficiente': [shapiro_valence__, shapiro_streams, statistic_mwu, spearman_corr],
                'Estatística/Coeficiente (Arredondado)': [shapiro_valence___rounded, shapiro_streams_rounded, statistic_mwu_rounded, spearman_corr_rounded],
                'p-valor/Intercepto Linear': [p_value_valence__, p_value_streams, p_value_mwu, p_value_spearman],
                'p-valor/Intercepto Linear (Arredondado)': [f'{p_value_valence__:.2f}', f'{p_value_streams:.2f}', f'{p_value_mwu:.2f}', f'{p_value_spearman:.2f}']
            }
            
            df_resultados = pd.DataFrame(resultados)
            
            display(df_resultados)
            ```
            
    - Tabela utilizada com dados limpos e novas variáveis:
        
        track_id: Identificador exclusivo da música. É um número inteiro de 7 dígitos que não se repete.
        
        track_name: Nome da música.
        
        artist(s)_name: Nome do(s) artista(s) da música.
        
        artist_count: Número de artistas que contribuíram na música.
        
        amount_of_tracks: Número total de músicas do artista na plataforma.
        
        total_streams: Soma de streams de todas as músicas dos artistas com mais de  uma música.
        
        relesead_date: União dos dados released_year + released_month + released_day que formam uma única coluna com as datas de lançamento.
        
        in_spotify_playlists: Número de listas de reprodução do Spotify em que a música está incluída
        
        in_apple_playlists: número de listas de reprodução da Apple Music em que a música está incluída.
        
        in_deezer_playlists: Número de playlists do Deezer em que a música está incluída.
        
        playlist_total: Número total de playlists de todas streams que a música está incluída.
        
        in_spotify_charts: Presença e posição da música nas paradas do Spotify
        
        in_apple_charts: Presença e classificação da música nas paradas da Apple Music.
        
        in_deezer_charts: Presença e posição da música nas paradas da Deezer.
        
        streams_clean: Número total de streams no Spotify. Representa o número de vezes que a música foi ouvida.
        
        bpm: Batidas por minuto, uma medida do tempo da música.
        
        bpm_quartil:  Valores de batidas por minuto divididos em 25%, 50%, 75% e 100%.
        
        bpm_categorizada: Valores de batidas por minuto categorizando os quartis em Alto e Baixo.
        
        danceability_%: Porcentagem que indica o quão apropriado a canção é para dançar.
        
        danceability_quartil: Valores de danceability divididos em 25%, 50%, 75% e 100%.
        
        danceability_categorizada: Valores de danceability categorizando os quartis em Alto e Baixo.
        
        valence_%: Positividade do conteúdo musical da música.
        
        valence_quartil: Valores de valence divididos em 25%, 50%, 75% e 100%.
        
        valence_categorizada: Valores de valence categorizando os quartis em Alto e Baixo.
        
        energy_%: Nível de energia percebido da música.
        
        energy_quartil: Valores de energy divididos em 25%, 50%, 75% e 100%.
        
        energy_categorizada: Valores de energy categorizando os quartis em Alto e Baixo.
        
        acusticness_%: Quantidade de som acústico na música.
        
        acousticness_quartil: Valores de acousticness divididos em 25%, 50%, 75% e 100%.
        
        acousticness_categorizada: Valores de acousticness categorizando os quartis em Alto e Baixo.
        
        instrumentality_%: Quantidade de conteúdo instrumental na música.
        
        instrumentalness_quartil: Valores de instrumentalness divididos em 25%, 50%, 75% e 100%.
        
        instrumentalness_categorizada: Valores de instrumentalness categorizando os quartis em Alto e Baixo.
        
        liveness_%: Presença de elementos de performance ao vivo.
        
        liveness_quartil: Valores de liveness divididos em 25%, 50%, 75% e 100%.
        
        liveness_categorizada: Valores de liveness categorizando os quartis em Alto e Baixo.
        
        speechiness_%: Quantidade de palavras faladas na música.
        
        speechiness_quartil: Valores de speechiness divididos em 25%, 50%, 75% e 100%.
        
        speechiness_categorizada: Valores de speechiness categorizando os quartis em Alto e Baixo.
        
- **Resultados e Conclusões:**
    - Medidas de tendência central
        
        Analisando as medidas de tendência central realizadas notamos que os valores de mediana estão abaixo dos valores de média, indicando uma assimétria para a direita, ou seja, alguns artistas estão com dados acima da maioria e são outliners. E na análise do histograma temos esta confirmação com uma cauda maior à direita, inclusive apresentando o outliner.
        
    - Medidas de dispersão
        
        Com os dados de Streams avaliamos o comportamento em relação ao ano de lançamento das músicas e notamos o maior número de streams para músicas lançadas no ano de 2017 e no total de playlist o maior desvio padrão foi para músicas do ano de 1991.
        
        Avaliando a medida de dispersão o Desvio Padrão é maior do que os valores de média das duas variáveis, apresentando maior variabilidade dos dados, não havendo tantas similaridades, ou seja, os dados apresentam maior dispersão.
        
    - Correlação de Pearson no Power BI
        - Streams x Playlist: Obteve-se um resultado positivo forte, o que é possível inferir que existe uma relação entre estar em um maior número de playlists gera um maior número de streams, sendo, neste contexto, uma hipótese confirmada.
        - Total de músicas x Streams: Apresentou uma correlação alta os valores, entretanto importante ressaltar que foi alto o resultado devido aos outliers e também devido a amostra utilizada. A amostra contava somente com as músicas mais escutadas referente ao ano de 2023, e não a todas as músicas lançadas pelos artistas analisados. Neste sentido, refutamos a hipótese.
    - Hipótese I
        
        O coeficiente de correlação indica que existe uma correlação fraca entre os valores de Batidas Por Minuto da categoria alto e o número de streams. Isso significa que, embora haja uma tendência de músicas com BPM alto terem mais streams, essa tendência não é muito forte.
        O valor p desta correlação é significativo. Isso significa que existe uma baixa probabilidade de que a correlação observada seja devido ao acaso. Em outras palavras, podemos inferir que há uma correlação fraca entre a característica de BPM alto e o número de streams.
        
        Com base nos resultados obtidos, esta hipótese é refutada, pois o dado obtido não apresenta força o suficiente para afirmar que estas características sozinhas levam uma música ao sucesso.
        
    - Hipótese II
        
        A correlação de Spearman entre os rankings do Spotify com os rankings da Apple Music e do ranking do Spotify com os rankings do Deezer são positivas e significativas. 
        O valor-p muito baixo indica que esta correlação provavelmente não ocorre ao acaso. No entanto, é importante ressaltar que a correlação de Spearman não implica causalidade direta. 
        
        Com base nos resultados, esta hipótese é afirmada, apesar de entender que estas variáveis sozinhas não possuem o poder de designar o caminho do sucesso de um novo artista.
        
    - Hipótese III
        
        Esses dados revelam uma correlação **bastante forte** entre o total de playlists do Spotify e o total de streams. Além disso, o valor-p **extremamente baixo** indica que essa correlação é **altamente significativa** estatisticamente, sugerindo que essa associação **não é provável de ocorrer ao acaso**.
        
        Essa **forte correlação** sugere que há uma **relação positiva** entre o número de playlists criadas e o número total de streams no Spotify. Isso pode indicar que um *aumento no número de playlists está associado a um aumento no número total de streams, ou vice-versa*. No entanto, é importante notar que correlação não implica causalidade direta, ou seja, *não podemos concluir que criar mais playlists diretamente causa um aumento nos streams ou vice-versa*. 
        
        Com base nos resultados obtidos, a hipótese foi confirmada. Importante ressaltar que esta hipótese unicamente não pode ser conderada como um único caminho para alcançar  sucesso. 
        
    - Hipótese IV
        
        Os dados indicam uma correlação muito forte entre o número de músicas de um artista e o total de streams de suas músicas. Além disso, o valor-p extremamente baixo indica que essa correlação é altamente significativa estatisticamente, sugerindo que essa associação não é provável de ocorrer ao acaso.
        Essa forte correlação sugere que artistas com um grande número de músicas tendem a acumular mais streams no total em comparação com artistas que têm menos músicas disponíveis. No entanto, é importante notar que correlação não implica causalidade direta. Assim, não podemos afirmar que o aumento do número de músicas de um artista diretamente causa um aumento no total de streams, ou vice-versa. 
        
        Com base nos resultados obtidos, a hipótese foi parcialmente confirmada, pois as variáveis utilizadas foram de apenas uma amostra de artistas com mais de uma música lançada e ouvida no ano de 2023, além de não ser todas as músicas dos artistas consideradas na amostra estudada.
        
    - Hipótese V
        - BPM
            
            Os dados indicam que as características relacionadas às batidas por minuto tendem a seguir uma **distribuição normal**, sugerindo que essa métrica pode desempenhar um papel consistente na percepção do sucesso de uma música. No entanto, as informações sobre os streams **não mostram essa tendência**, indicando uma possível variabilidade nas preferências dos usuários. Além disso, o teste de Mann-Whitney U revela uma **diferença significativa** entre os grupos analisados, o que sugere que certas características musicais podem influenciar de maneira distinta o número de streams. No entanto, a **falta de correlação significativa** entre as variáveis testadas destacam a complexidade na determinação precisa do impacto das características da música no sucesso em termos de streams no Spotify.
            Com base nos resultados obtidos, a hipótese foi refutada, pois o teste de Mann-Whitney U apresenta uma variabilidade dos dados que pode indicar que os BPM podem influenciar em alguns streams, mas não é algo decisivo para o sucesso.
            
        - Dançabilidade - Danceability
            
            A dançabilidade das músicas tende a seguir uma **distribuição normal**, indicando que essa métrica pode desempenhar um papel **consistente** na avaliação do sucesso de uma música. No entanto, os dados de streams não mostram a mesma tendência, sugerindo uma possível variação nas preferências dos usuários em relação a diferentes características musicais.
            
            Além disso, o teste de Mann-Whitney U revela uma **diferença significativa** entre os grupos analisados, indicando que certas características musicais podem influenciar de maneira distinta o número de streams obtidos. No entanto, a correlação de Spearman apresenta um **coeficiente negativo**, embora não seja estatisticamente significativo, sugerindo uma **associação fraca ou inexistente** entre as características musicais e o número de streams.
            
            Com base nos resultados obtidos, a hipótese acima foi refutada, onde é possível inferir que a característica de dançabilidade não influencia na quantidade de streams da música.
            
        - Acústica - Acousticness
            
            O teste de Shapiro de Acústica revela que essa característica tende a seguir uma **distribuição normal**, sugerindo sua importância na percepção do êxito de uma música. No entanto, os dados de streams não apresentam a mesma regularidade, indicando uma possível diversidade nas preferências dos usuários em relação a diferentes aspectos musicais.
            
            O teste de Mann-Whitney U evidencia uma **diferença significativa** entre os grupos analisados, indicando que certas características musicais podem influenciar de forma distinta o número de streams. No entanto, a correlação de Spearman, embora **negativa**, **não é estatisticamente significativa**, o que sugere uma **associação fraca ou inexistente** entre esta característica musical e o número de streams.
            
            Com base nos resultados obtidos, a hipótese acima foi refutada, onde é possível inferir que a característica de acústica não influencia na quantidade de streams da música.
            
        - Energia - Energy
            
            O teste de Shapiro de Energia sugere que essa característica tende a seguir uma **distribuição normal**, o que ressalta sua importância na percepção da energia das músicas.
            
            O teste de Mann-Whitney U mostra uma **diferença significativa** entre os grupos analisados, indicando que características específicas das músicas podem influenciar de forma distinta o número de streams. No entanto, a correlação de Spearman revela uma **associação negativa**, embora não seja estatisticamente significativa, sugerindo que outras variáveis podem ter uma influência maior no número de streams. Isso sugere que a energia das músicas pode não ser um preditor confiável do sucesso em termos de streams no Spotify. Esses resultados destacam a complexidade na compreensão do comportamento dos usuários e na avaliação do impacto das características musicais no desempenho das músicas na plataforma.
            
            Com base nos resultados obtidos, a hipótese acima foi refutada, onde é possível inferir que a característica de energia não influencia na quantidade de streams da música.
            
        - Instrumentalidade - Instrumentalness
            
            O teste de Shapiro do Instrumental sugere que essa característica **não segue uma distribuição normal**, o que pode indicar uma variedade de preferências entre os usuários em relação à presença de elementos instrumentais nas músicas.
            
            O teste de Mann-Whitney U revela uma **diferença significativa** entre os grupos analisados, indicando que características musicais específicas podem influenciar de maneira distinta o número de streams. No entanto, a correlação de Spearman mostra uma **associação quase nula** entre as características musicais e o número de streams, sugerindo que outros fatores podem ter uma influência maior nesse resultado.
            
            Com base nos resultados obtidos, a hipótese acima foi refutada, onde é possível inferir que a característica de instrumentalidade não influencia na quantidade de streams da música.
            
        - Fala - Speechiness
            
            O teste de Shapiro da quantidade de palavras cantadas sugere que essa característica **não segue uma distribuição normal**, indicando uma variedade nas preferências dos usuários em relação à densidade das letras das músicas.
            
            O teste de Mann-Whitney U mostra uma **diferença significativa** entre os grupos analisados, o que indica que características específicas das músicas podem influenciar de forma distinta o número de streams. No entanto, a correlação de Spearman revela uma **associação negativa**, embora **não seja estatisticamente significativa**, sugerindo que outras variáveis podem ter uma influência maior no número de streams.
            
            Com base nos resultados obtidos, a hipótese acima foi refutada, onde é possível inferir que a característica de fala não influencia na quantidade de streams da música.
            
        - Vivacidade - Liveness
            
            O teste de Shapiro da Vivacidade sugere que essa característica **não segue uma distribuição normal**, indicando uma diversidade nas preferências dos usuários em relação à energia das músicas.
            
            O teste de Mann-Whitney U revela uma **diferença significativa** entre os grupos analisados, o que sugere que características específicas das músicas podem influenciar de forma distinta o número de streams. No entanto, a correlação de Spearman mostra uma **associação negativa**, embora não seja estatisticamente significativa, sugerindo que outras variáveis podem ter uma influência maior no número de streams.
            
            Com base nos resultados obtidos, a hipótese acima foi refutada, onde é possível inferir que a característica de vivacidade não influencia na quantidade de streams da música.
            
        - Valência - Valence
            
            O teste de Shapiro da Valência indica que essa característica tende a seguir uma **distribuição normal**, sugerindo sua importância na percepção do apelo emocional das músicas.
            
            O teste de Mann-Whitney U mostra uma **diferença significativa** entre os grupos analisados, o que sugere que características específicas das músicas podem influenciar de maneira distinta o número de streams. No entanto, a correlação de Spearman indica uma **associação negativa**, embora não seja estatisticamente significativa, sugerindo que outras variáveis podem ter uma influência maior no número de streams.
            
            Com base nos resultados obtidos, a hipótese acima foi refutada, onde é possível inferir que a característica de valência não influencia na quantidade de streams da música.
            
- **Limitações/Próximos Passos:**
    
    Uma importante lacuna encontrada foi a de falta de conhecimento do grupo musical a ser lançado. Como não foi apresentado seu público alvo e objetivos de negócio, as análises foram generalizadas e não focadas nas necessidades do grupo. 
    
    A base de dados analisada foi limitante em diversos pontos, dentre eles a organização das variáveis categóricas, pois os nomes de artistas e das músicas ficaram desorganizados e alguns inelegíveis possuindo caractéres não identificados, nomes de música com descrição desnecessária ou bagunçada, nome de artistas não claros, entre outros. Isso dificultou colocar uma padronização que não desconfigurasse esses dados, causando até mesmo, dados nulos após a limpeza com SQL.
    
    A falta de outras variáveis como gêneros e subgêneros musicais, nacionalidade de origem do artista, público alvo e idioma das músicas podem ter contribuido para não alcançar respostas mais precisas sobre as hipóteses levantadas. 
    
    Os gêneros e subgêneros musicais poderiam ajudar a entender os tipos de playlists mais escutadas e de mais sucesso, e também entender quais características técnicas das músicas são mais apreciadas pelos ouvintes nesses grupos.
    
    O idioma e nacionalidade das músicas poderiam contribuir na hipótese de charts para identificar quais deles costumam estar no topo e saber a diferença entre as diferentes plataformas de streams.
    
    O público alvo pode determinar as decisões do grupo musical a ser lançado, auxiliando na hipótese de artistas com mais músicas ou até mesmo gerando uma outra hipótese para responder a seguinte pergunta: Quais gêneros e subgêneros tem mais streams por determinados públicos-alvo? Esta pergunta unida com as informações do grupo que será lançado poderiam nortear melhores decisões, visto que seria possível entender qual público alvo o novo grupo deveria colocar seus esforços de criação e marketing.