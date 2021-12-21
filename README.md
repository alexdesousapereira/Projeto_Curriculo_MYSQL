# INTRODUÇÃO
***
 A empresa Alpha X deseja fazer um processo seletivo para uma vaga de DBA com o candidato Alex de Sousa Pereira. Desta forma, como desafio Alex deverá criar um banco de dados relacional relacionado há alguma área especifica. Então, o candidato ideal deverá utilizar de toda sua criatividade e conhecimentos técnicos para criação do banco de dados.

 # OBJETIVO
 ***
O objetivo deste projeto e demonstrar como será construído o banco de dados relacional para a vaga da empresa Alpha X. De modo que, o banco de dados foi desenvolvido com base no currículo do candidato Alex. Para tanto, os scripts utilizados foram construídos para o Banco de Dados MySQL. Contudo, propõese que o banco seja modelado como segue a imagem abaixo:

[Curriculo](https://i.imgur.com/X8pMndU.png)

Deste modo, a partir do modelo mencionado propõe-se responder as seguintes perguntas:

1-) NÚMERO DE ATIVIDADES EXERCIDAS DO CANDIDATO EM CADA EMPRESA;

2-) FORMAS DE CONTATO DO CANDIDATO;

3-) CURRÍCULO COM AS INFORMAÇÕES NOME, SEXO, IDADE, TELEFONE, EMAIL,EMPRESAS E SEU NÚMERO DE HABILIDADES;

4-) HABILIDADES DO CANDIDATO COM BASE EM UM NÍVEL DESEJADO.

Contudo, o projeto será definido em 6 etapas:

1-) Construção da estrutura das tabelas;

2-) Chaves primarias e secundarias das tabelas;

2-) Construção de Tabela Backup dos dados;

3-) Dicíonário de Dados das Tabelas.

3-) Inserção dos dados nas tabelas;

4-) Criação das querys para responder as perguntas;

## ETAPA 01
***
Nesta primeira etapa, criare-mos a estrutura de nossas tabelas com base no modelo conceitual pré definido!

VAMOS COMEÇAR CRIANDO NOSSO DATABASE CURRÍCULO: 
```
 	CREATE DATABASE CURRICULO;
```
USANDO DATABASE CURRICULO:
```
    USE CURRICULO;
```
 CRIANDO TABELAS BASEANDO NO PROBLEMA DE NEGÓCIOS 

 OBS.: IREMOS INSERIR AS FOREIGN KEYS DEPOIS DA CRIAÇÃO DA TABELA, AFIM DE TERMOS UM DICIONÁRIO DE DADOS.

CRIANDO TABELA CANDIDATO:
```
    CREATE TABLE CANDIDATO(
        IDCANDIDATO INT PRIMARY KEY AUTO_INCREMENT,
        NOME VARCHAR(30) NOT NULL,
        SEXO ENUM('M', 'F') NOT NULL,
        IDADE INT NOT NULL
    );
```
CRIANDO TABELA ENDEREÇO:
```
    CREATE TABLE ENDERECO(
        IDENDERECO INT PRIMARY KEY AUTO_INCREMENT,
        RUA_AVENIDA VARCHAR(100) NOT NULL,
        BAIRRO VARCHAR(30) NOT NULL,
        CIDADE VARCHAR(30) NOT NULL,
        ESTADO CHAR(2) NOT NULL,
        ID_CANDIDATO INT UNIQUE 
    );
```
CRIANDO TABELA CONTATO:
```
    CREATE TABLE CONTATO(
        IDCONTATO INT PRIMARY KEY AUTO_INCREMENT,
        TELEFONE VARCHAR(30) NOT NULL,
        EMAIL VARCHAR(100) NOT NULL,
        PORTFOLIO VARCHAR(100),
        LINKEDIN VARCHAR(100),
        ID_CANDIDATO INT UNIQUE
    );
```
CRIANDO TABELA HABILIDADES:
```
    CREATE TABLE HABILIDADES(
        IDHABILIDADE INT PRIMARY KEY AUTO_INCREMENT,
        TIPO VARCHAR(30) NOT NULL,
        NIVEL ENUM('BÁSICO', 'INTERMEDIÁRIO', 'AVANÇADO') NOT NULL,
        ID_CANDIDATO INT 
    );
```
RENOMEANDO CAMPO DA TABELA:
```	
	ALTER TABLE HABILIDADES
	CHANGE TIPO HABILIDADE VARCHAR(30) NOT NULL;
```
VERIFICANDO:
```
	SHOW COLUMNS FROM HABILIDADES;
```
CRIANDO TABELA EDUCAÇÃO:
```
    CREATE TABLE EDUCACAO(
        IDEDUCACAO INT PRIMARY KEY AUTO_INCREMENT,
        INICIO DATE NOT NULL,
        FIM DATE NOT NULL,
        INSTITUICAO VARCHAR(50) NOT NULL,
        FORMACAO_CURSO VARCHAR(100) NOT NULL,
        ID_CANDIDATO INT
    );
```
CRIANDO TABELA EXPERIÊNCIA:
```
    CREATE TABLE EXPERIENCIA(
        IDEXPERIENCIA INT PRIMARY KEY AUTO_INCREMENT,
        EMPRESA VARCHAR(50) NOT NULL,
        INICIO DATE NOT NULL,
        FIM DATE NOT NULL,
        ID_CANDIDATO INT
    );
```
CRIANDO TABELA ATIVIDADES:
```
    CREATE TABLE ATIVIDADES(
        IDATIVIDADE INT PRIMARY KEY AUTO_INCREMENT,
        ATIVIDADE VARCHAR(50)
    );

    ALTER TABLE ATIVIDADES
    MODIFY ATIVIDADE VARCHAR(75) NOT NULL;
```
AGORA IREMOS CRIAR UMA TABELA ASSOCIATIVA AFIM DE ELIMINAR A CARDIALIDADE N x N ENTRE EXPERIÊNCIA x ATIVIDADES.
```
CREATE TABLE EXPERIENCIA_ATIVIDADE(
	ID_EXPERIENCIA INT,
	ID_ATIVIDADE INT,
	PRIMARY KEY(ID_EXPERIENCIA, ID_ATIVIDADE)
);
```
## ETAPA 02
***
Continuando o projeto, nesta etapa iremos criar as chaves secúndarias que relacionam as tabelas como no modelo pré-definido.

