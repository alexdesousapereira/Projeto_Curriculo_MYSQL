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

3-) Construção de Tabela Backup dos dados;

4-) Dicíonário de Dados das Tabelas.

5-) Inserção dos dados nas tabelas;

6-) Criação das querys para responder as perguntas;

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
```
MODIFICANDO CAMPO DA TABELA:
```
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
Continuando o projeto, nesta etapa iremos criar as chaves secúndarias que relacionam tabela com tabela como no modelo conceitual pré-definido.

CRIAR AS FOREIGNS KEYS REFERENTES A CADA TABELA:

ENDEREÇO:
```
    ALTER TABLE ENDERECO
    ADD CONSTRAINT FK_ENDERECO_CANDIDATO
    FOREIGN KEY(ID_CANDIDATO)
    REFERENCES CANDIDATO(IDCANDIDATO);
```

CONTATO:
```
    ALTER TABLE CONTATO
    ADD CONSTRAINT FK_CONTATO_CANDIDATO
    FOREIGN KEY(ID_CANDIDATO)
    REFERENCES CANDIDATO(IDCANDIDATO);
```

EDUCAÇÃO:
```
    ALTER TABLE EDUCACAO
    ADD CONSTRAINT FK_EDUCACAO_CANDITATO
    FOREIGN KEY(ID_CANDIDATO)
    REFERENCES CANDIDATO(IDCANDIDATO);
```

EXPERIÊNCIA:
```
    ALTER TABLE EXPERIENCIA_ATIVIDADE
    ADD CONSTRAINT FK_EXPERIENCIA	
    FOREIGN KEY(ID_EXPERIENCIA)
    REFERENCES EXPERIENCIA(IDEXPERIENCIA);
```

ATIVIDADES:
```
    ALTER TABLE EXPERIENCIA_ATIVIDADE
    ADD CONSTRAINT FK_ATIVIDADE
    FOREIGN KEY(ID_ATIVIDADE)
    REFERENCES ATIVIDADES(IDATIVIDADE);
```
## ETAPA 03
Na terceira etapa, iremos construir a tabela de backup dos dados.

CONTRUINDO DATABASE BACKUP:
```
CREATE DATABASE BACKUP;
USE BACKUP;
```
CONSTRUINDO TABELA BACKUP DOS CANDIDATOS:
```
CREATE TABLE INFO_CANDIDATOS(
	IDBACKUP INT PRIMARY KEY AUTO_INCREMENT,
	IDCANDIDATO INT,
	NOME VARCHAR(30),
	SEXO CHAR(1),
	IDADE INT,
	DATA_HORA DATETIME,
	USUARIO VARCHAR(30),
	EVENTO CHAR(1)
);
```

TRIGGER SOBRE QUANDO FORAM INSERIDAS AS INFORMAÇÕES DOS CANDIDATOS:
```
DELIMITER #
	CREATE TRIGGER BACKUP_INFO_CANDIDATO_INSERT
	AFTER INSERT ON CANDIDATO
	FOR EACH ROW

	BEGIN

		INSERT INTO BACKUP.INFO_CANDIDATOS VALUES(NULL, NEW.IDCANDIDATO, NEW.NOME, NEW.SEXO, NEW.IDADE, NOW(),CURRENT_USER(),"I");

	END
	# 
DELIMITER ;
```
TRIGGER SOBRE QUANDO FORAM DELETADAS AS INFORMAÇÕES DOS CANDIDATOS:
```
DELIMITER #
	CREATE TRIGGER BACKUP_INFO_CANDIDATO_DELETE
	BEFORE DELETE ON CANDIDATO
	FOR EACH ROW

	BEGIN

		INSERT INTO BACKUP.INFO_CANDIDATOS VALUES(NULL, OLD.IDCANDIDATO, OLD.NOME, OLD.SEXO, OLD.IDADE, NOW(), CURRENT_USER(),"D");

	END
	# 
DELIMITER ;
``` 
TRIGGER SOBRE QUANDO FORAM ATUALIZADAS AS INFORMAÇÕES DOS CANDIDATOS:
```
DELIMITER #    
    CREATE TRIGGER BACKUP_INFO_CANDIDATO_UPDATE
	AFTER UPDATE ON CANDIDATO
	FOR EACH ROW

	BEGIN

		INSERT INTO BACKUP.INFO_CANDIDATOS VALUES(NULL, NEW.IDCANDIDATO, OLD.NOME, OLD.SEXO, OLD.IDADE, NOW(), CURRENT_USER(),"U");

	END
	#
DELIMITER ;
```
## ETAPA 04
***
Nesta quarta etapa, iremos criar um dicionário de dados com a finalidade de demonstrar como se comportam os campos de nossas tabelas.

MOSTRANDO O DICIONÁRIO DE DADOS
```
SHOW DATABASES;
```
| DATABASES          |
| ---                |
| Database           |
| information_schema |
| backup             |
| curriculo          |
| mysql              |
| performance_schema |
| sakila             |
| sys                |
| world              |

```
USE INFORMATION_SCHEMA;

STATUS; 

SHOW TABLES;

DESC TABLE_CONSTRAINTS;

SELECT CONSTRAINT_SCHEMA AS "BANCO",
	   TABLE_NAME AS "TABELA",
	   CONSTRAINT_NAME AS "NOME REGRA",
	   CONSTRAINT_TYPE AS "TIPO"
FROM TABLE_CONSTRAINTS
	WHERE CONSTRAINT_SCHEMA = 'CURRICULO';
```

| BANCO     | TABELA                | NOME REGRA            | TIPO        |
| ---       | ---                   | ---                   | ---         |
| curriculo | atividades            | PRIMARY               | PRIMARY KEY |
| curriculo | candidato             | PRIMARY               | PRIMARY KEY |
| curriculo | contato               | PRIMARY               | PRIMARY KEY |
| curriculo | contato               | ID_CANDIDATO          | UNIQUE      |
| curriculo | contato               | FK_CONTATO_CANDIDATO  | FOREIGN KEY |
| curriculo | educacao              | PRIMARY               | PRIMARY KEY |
| curriculo | educacao              | FK_EDUCACAO_CANDITATO | FOREIGN KEY |
| curriculo | endereco              | PRIMARY               | PRIMARY KEY |
| curriculo | endereco              | ID_CANDIDATO          | UNIQUE      |
| curriculo | endereco              | FK_ENDERECO_CANDIDATO | FOREIGN KEY |
| curriculo | experiencia           | PRIMARY               | PRIMARY KEY |
| curriculo | experiencia_atividade | PRIMARY               | PRIMARY KEY |
| curriculo | experiencia_atividade | FK_ATIVIDADE          | FOREIGN KEY |
| curriculo | experiencia_atividade | FK_EXPERIENCIA        | FOREIGN KEY |
| curriculo | habilidades           | PRIMARY               | PRIMARY KEY |

