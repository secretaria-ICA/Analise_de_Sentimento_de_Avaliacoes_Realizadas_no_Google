# Projeto Final do Curso CCE PUC-Rio - BI MASTER 2019.2

**Análise de Sentimento de Avaliações Realizadas no Google**

**Aluno: [Daniel Portugal](https://github.com/danielportugalHTW/PROJ_BI_MASTER) - Matrícula: 192.190.088**

**Orientador: [Leonardo Forero](leofome@gmail.com)**

**Trabalho apresentado ao curso [BI MASTER](https://ica.puc-rio.ai/bi-master) como pré-requisito para conclusão de "Curso de Pós Graduação Business Intelligence Master" na Pontifícia Universidade Católica do Rio de Janeiro**


### **Resumo**

Com a maior acessibilidade da internet, principalmente pelo crescimento das redes sociais, pequenas empresas e profissionais de diversas áreas podem divulgar seus produtos e serviços com maior alcance e baixo custo, descentralizando o marketing das grandes mídias.

No entanto, da mesma maneira que isso facilita a divulgação de novos negócios, tornam públicas também a qualidade deles, podendo até destruir uma reputação.  

Este trabalho visa gerar uma base de dados de avaliações positivas e negativas expostas na internet, possibilitando identificar possíveis pontos 
de melhorias.

### **1. Introdução**

A **Clínica Odontológica Juliana Fernandes** possui 10 anos de existência e sempre teve como objetivo oferecer a seus pacientes uma experiência diferenciada nos tratamentos odontológicos. 

Sabendo que muitas pessoas apresentam traumas e medos de Dentista, a clínica busca constantemente um atendimento humanizado e personalizado para desconstruir esse lado negativo e melhorar positivamente a experiência dessas pessoas. 

Para isso, a clínica tem como pilares o conforto, a elegância, a ética, a qualidade e a tecnologia. Dessa forma, é de extrema importância que a experiência do cliente seja excepcional desde o primeiro contato com a clínica. 

Portanto, identificar cedo os pontos de melhorias é fundamental para aperfeiçoar o atendimento e garantir a missão da clínica. 

### **2. Modelagem**

#### **2.1 Objetivo**

O objetivo do projeto é identificar se o comentário/avaliação é positiva ou negativa para o negócio.

#### **2.2 Premissas**

- Os dados são classificados em Positivo ou Negativo;
- A base de dados foi extraída de avaliações de clínicas no "google meu negócio";

#### **2.3 Aplicação e Codificação** 

Para esta aplicação, iremos utilizar a biblioteca nltk para trabalhar com dados textuais e sklearn com suas bibliotecas e pacotes, que proporcionam muita simplicidade as aplicações, além de garantir scripts descomplicados e eficientes. A base de dados está armazenada no google drive e para acessál-a iremos utilizar as bibliotecas do google.

Abaixo iremos apresentar o passo a passo dos comandos:

#### **2.3.1 Instalação dos pacotes** 

    import nltk
    import re
    import os
    import string
    import pandas as pd    

    from sklearn.feature_extraction.text import CountVectorizer
    from sklearn.naive_bayes             import MultinomialNB
    from sklearn                         import metrics
    from sklearn.model_selection         import cross_val_predict
    from sklearn.ensemble                import RandomForestClassifier
    from google.colab                    import drive
    from nltk.tokenize                   import word_tokenize

#### **2.3.2 Uploud da base de dados** 

    drive.mount('/content/drive')
    orkdir_path = '/content/drive/My Drive'
    os.chdir(workdir_path)
    dados = pd.read_csv('Base_Consultorio_Odonto.csv', sep = ';')

#### **2.3.3 Tratamento dos dados** 

    lower_alpha = lambda x: re.sub(r"""\w*\d\w*""", ' ', x.lower())
    dados['Descricao'] = dados.Descricao.map(lower_alpha)
    punc_re = lambda x: re.sub('[%s]' % re.escape(string.punctuation), ' ', x)
    dados['Descricao'] = dados.Descricao.map(punc_re)
    
#### **2.3.4 Tokenize**     

    nltk.download('punkt')
    dados['tokens'] = dados.Descricao.map(word_tokenize)

#### **2.3.5 Stop Words**     

    nltk.download("stopwords")
    stop_words = stopwords.words('portuguese')
    stop_lambda = lambda x: [y for y in x if y not in stop_words]
    dados['tokens_stop'] = dados.tokens.apply(stop_lambda)

#### **2.3.6 Wordcloud avaliações positivas** 

![Wordcloud](https://github.com/danielportugalHTW/PROJ_BI_MASTER/blob/main/positive.PNG)

#### **2.3.7 Wordcloud avaliações negativas**  

![Wordcloud](https://github.com/danielportugalHTW/PROJ_BI_MASTER/blob/main/negative.PNG)

#### **2.3.8 Separando as avaliações**  

    comentarios = dados["Descricao"].values

#### **2.3.9 Separando as classes**  

    classes = dados["Classificação"].values
    
#### **2.3.10 Bag of words**      

    vectorizer = CountVectorizer(analyzer = "word", tokenizer = None, preprocessor = None,stop_words = stop_words, max_features = 5000)

#### **2.3.11 Treinar o modelo, aprender o vocabulário e transformar nossos dados de treinamento em feature vectors**    

    train_data_features = vectorizer.fit_transform(comentarios)    
    
#### **2.3.12 Classificador Random Forest**        

    forest = RandomForestClassifier(n_estimators = 100)
    
 #### **2.3.13 Ajusta a forest ao dataset de treinamento usando a bag of words como feature e os sentimentos como a resposta variável**   
 
    forest = forest.fit(train_data_features, classes)
    
### **3. Resultados**     

    resultados = forest.predict(test_data_features_resultados)
    metrics.accuracy_score(classes, resultados)
    
![Confusion](https://github.com/danielportugalHTW/PROJ_BI_MASTER/blob/main/confusion.PNG)
    
### **4. Conclusões**         

O trabalho teve o objetivo de demonstrar como conseguir classificar a qualidade do seu negócio de acordo com as avaliaçõesdos clientes realizadas na internet.

Foram testados 3 metodologias de previsão: Naive Bayes Multinomial, Bigrams e Random Forest.

A classificador Random Forest apresentou um aproveitamento melhor alcançando uma acurácia de 1.0.

Com uma simples combinação de aprendizado adquirido durante o curso Bi-Master podemos observar o quanto valioso podemos gerar de insights para clientes e como no meu caso, para o meu próprio negócio.

O próximo objetivo será a criação de um crawler para capturar as avaliações dos nossos concorrentes para assim aprender e melhorar minha própria empresa com os erros e acertos obtidos e processados.











