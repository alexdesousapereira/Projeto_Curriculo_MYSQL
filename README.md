# INTRODUÇÃO
***
 A empresa Alpha X deseja fazer um processo seletivo para uma vaga de DBA com o candidato Alex de Sousa Pereira. Desta forma, como desafio Alex deverá criar um banco de dados relacional relacionado há alguma área especifica. Então, o candidato ideal deverá utilizar de toda sua criatividade e conhecimentos técnicos para criação de um banco de dados.

 # OBJETIVO
 ***
O objetivo deste projeto e demonstrar como será construído o banco de dados relacional para a vaga na empresa Alpha X. De modo que, o banco de dados foi desenvolvido com base no currículo do candidato Alex. Para tanto, os scripts utilizados foram construídos voltados a ao Banco de Dados MySQL. Contudo, propõe-se que o banco seja modelado como segue o modelo conceitual abaixo:

![modelo conceitual](https://i.imgur.com/iw9JnMn.png)

Deste modo, a partir do modelo mencionado propõe-se responder as seguintes perguntas:

1-) Número de atividades exercidas do candidato em cada empresa;

2-) Formas de contato do candidato;

3-) Currículo com as informações nome, sexo, idade, telefone, e-mail, empresas e seu número de habilidades;

4-) Habilidades do candidato com base em um nível desejado.

Contudo, o projeto será definido em 6 etapas:

1-) Construção da estrutura das tabelas;

2-) Chaves primarias e secundarias das tabelas;

3-) Construção de Tabela Backup dos dados;

4-) Dicíonário de Dados das Tabelas.

5-) Inserção dos dados nas tabelas;

6-) Criação das querys para responder as perguntas;

## ETAPA 01
***
Nesta primeira etapa, criaremos a estrutura de nossas tabelas com base no modelo conceitual pré definido!

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

CRIANDO AS FOREIGNS KEYS REFERENTES A CADA TABELA:

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

## ETAPA 05
***
Nesta etapa, iremos fazer a inserção dos dados nas tabelas.
INSERINDO DADOS DO CANDIDATO:
```
INSERT INTO CANDIDATO(IDCANDIDATO,NOME,SEXO,IDADE) VALUES(NULL, 'ALEX', 'M', 21);
```
INSERINDO DADOS DE ENDEREÇO:
```
INSERT INTO ENDERECO VALUES(NULL, 'RUA MACHADO', 'CHAPADA', 'ELÓI MENDES', 'MG', 1);
```
INSERINDO DADOS DE CONTATO:
```
INSERT INTO CONTATO VALUES(NULL, "35992114883",'alexdesousapereiraa@gmail.com','https://github.com/alexdesousapereira','https://www.linkedin.com/in/alex-pereira-14b798169/',1);
```
INSERINDO DADOS DE EDUCAÇÃO:
```
INSERT INTO EDUCACAO VALUES(NULL, 15/03/2018, 15/09/2021, 'UNIFAL', 'BACHARELADO INTERDICIPLINAR EM CIÊNCIA E ECONOMIA', 1);

INSERT INTO EDUCACAO VALUES(NULL, 15/03/2018, 15/09/2022, 'UNIFAL', 'BACHARELADO EM CIÊNCIAS ATUARIAIS', 1);

INSERT INTO EDUCACAO VALUES(NULL, 25/02/2021, 07/05/2021, 'IGTI', 'BOOTCAMP DESENVOLVEDOR BUSINESS INTELLIGENCE', 1);

INSERT INTO EDUCACAO VALUES(NULL, 05/01/2021, 13/05/2021, 'DSA', 'MICROSOFT POWER BI PARA DATA SCIENCE, Versão 2.0', 1);
```
INSERINDO DADOS DE HABILIDADES:
```
INSERT INTO HABILIDADES VALUES(NULL, "Criação de Dashboards" ,"INTERMEDIÁRIO" ,1);

INSERT INTO HABILIDADES VALUES(NULL, "Excel" ,"AVANÇADO" ,1);

INSERT INTO HABILIDADES VALUES(NULL, "Pentaho" ,"INTERMEDIÁRIO" ,1);

INSERT INTO HABILIDADES VALUES(NULL, "R","BÁSICO",1);

INSERT INTO HABILIDADES VALUES(NULL, "Python","BÁSICO",1);
```
INSERINDO DADOS  DE EXPERIÊNCIAS:
```
INSERT INTO EXPERIENCIA VALUES(NULL, 'COOPER STRANDARD', 16/07/2018, 01/06/2020, 1);

INSERT INTO EXPERIENCIA VALUES(NULL, 'U.EXPERIENCE', 09/11/2021, NOW(), 1)
```
INSERINDO DADOS DAS ATIVIDADES:
```
INSERT INTO ATIVIDADES VALUES(NULL, "GERENCIAMENTO DO PROGRAMA DE SUGESTÕES");

INSERT INTO ATIVIDADES VALUES(NULL, "LANÇAMENTO EM SISTEMAS (OEE E 5S)");

INSERT INTO ATIVIDADES VALUES(NULL, "DESENVOLVIMENTO E ANÁLISE DE RELATÓRIOS NO EXCEL");

INSERT INTO ATIVIDADES VALUES(NULL, "AUXILIAR NA ELABORAÇÃO DE KAIZEN");

INSERT INTO ATIVIDADES VALUES(NULL, "AUXILIAR EM PROCESSOS ADMINISTRATIVOS");

INSERT INTO ATIVIDADES VALUES(NULL, "CRIAÇÃO DE DASHBOARD NO GOOGLE DATA STUDIO");

INSERT INTO ATIVIDADES VALUES(NULL, "AUTOMATIZAÇÃO DE PROCESSOS COM PYTHON");

INSERT INTO ATIVIDADES VALUES(NULL, "LIMPEZA E TRATAMENTO DE DADOS");

INSERT INTO ATIVIDADES VALUES(NULL, "REALIZAÇÃO DE CÁLCULOS E MODELOS ESTATÍSTICOS");

INSERT INTO ATIVIDADES VALUES(NULL, "REGISTRAR DOCUMENTAÇÕES DOS PROJETOS DESENVOLVIDOS");
```
INSERINDO DADOS DA TABELA ASSOCIATIVA EXPERIENCIA_ATIVIDADE:
```
INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(1,1);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(1,2);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(1,3);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(1,4);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(1,5);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(2,3);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(2,6);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(2,7);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(2,8);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(2,9);

INSERT INTO EXPERIENCIA_ATIVIDADE VALUES(2,10);
```
## ETAPA 06
Nesta etapa, criaremos as querys para responder as nossas perguntas:

 1-) NÚMERO DE ATIVIDADES EXERCIDAS DO CANDIDATO EM CADA EMPRESA.
```
SELECT 
	EMP.EMPRESA, 
	COUNT(ATI.ATIVIDADE) AS Total_de_Atividades
FROM EXPERIENCIA_ATIVIDADE
	INNER JOIN ATIVIDADES as ATI
	ON ID_ATIVIDADE = IDATIVIDADE
	INNER JOIN EXPERIENCIA as EMP
	ON ID_EXPERIENCIA = IDEXPERIENCIA
GROUP BY EMP.EMPRESA;
```
| EMPRESA          | Total_de_Atividades |
| ---              | ---                 |
| COOPER STRANDARD |                   5 |
| U.EXPERIENCE     |                   6 |


2-)  FORMAS DE CONTATO DO CANDIDATO
```
SELECT 
	NOME,
	TELEFONE,
	EMAIL,
	PORTFOLIO,
	LINKEDIN
FROM CONTATO
	RIGHT JOIN CANDIDATO
	ON IDCANDIDATO = ID_CANDIDATO;
```
| NOME     | TELEFONE    | EMAIL                         | PORTFOLIO                             | LINKEDIN                                |
| ---      | ---         | ---                           | ---                                   | ---                                     |
| ALEX     | 35992114883 | alexdesousapereiraa@gmail.com | https://github.com/alexdesousapereira | https://www.linkedin.com/in/alex-pereira-14b798169/ |


3-)  CURRÍCULO COM AS INFORMAÇÕES NOME, SEXO, IDADE, TELEFONE, EMAIL,EMPRESAS E SEU NÚMERO DE HABILIDADES.
```
SELECT 
	NOME,
	SEXO,
	IDADE,
	C.TELEFONE, 
	C.EMAIL,
	E.EMPRESA,
	COUNT(H.HABILIDADE) as TOTAL_DE_HABILIDADES
FROM CANDIDATO
	LEFT JOIN CONTATO AS C
	ON IDCANDIDATO = C.ID_CANDIDATO
	LEFT JOIN HABILIDADES AS H
	ON IDCANDIDATO = H.ID_CANDIDATO
	LEFT JOIN EXPERIENCIA AS E
	ON IDCANDIDATO = E.ID_CANDIDATO
GROUP BY NOME, SEXO, IDADE, C.TELEFONE, C.EMAIL, E.EMPRESA;
```

| NOME     | SEXO | IDADE | TELEFONE    | EMAIL                         | EMPRESA                             | TOTAL_DE_HABILIDADES |
| ---      | ---  | ---   | ---         | ---                           | ---                                 | ---                  |
| ALEX     | M    |    21 | 35992114883 | alexdesousapereiraa@gmail.com | COOPER STRANDARD |                    5 |
| ALEX     | M    |    21 | 35992114883 | alexdesousapereiraa@gmail.com | U.EXPERIENCE     |                    5 |

4-) QUERY QUE TRAGA AS HABILIDADES DO CANDIDATO COM BASE EM UM NÍVEL DESEJADO.

PARA FAZER ISTO, IREMOS CRIAR UMA PROCEDURE:
```
DELIMITER $

CREATE PROCEDURE NIVEL_HABILIDADE(P_NIVEL ENUM('BÁSICO', 'INTERMEDIÁRIO', 'AVANÇADO'))

BEGIN 
	
	SELECT HABILIDADE
	FROM HABILIDADES WHERE NIVEL = P_NIVEL;

END
$

DELIMITER ;
```
HABILIDADES DE NÍVEL BÁSICO:
```
CALL NIVEL_HABILIDADE('BÁSICO');
```
| HABILIDADE |
| ---        |
| R          |
| Python     |

HABILIDADES DE NÍVEL INTERMEDIÁRIO:
```
CALL NIVEL_HABILIDADE('INTERMEDIÁRIO');
```

| HABILIDADE              |
| ---                     |
| Criação de Dashboards   |
| Pentaho                 |

HABILIDADES DE NÍVEL AVANÇADO:

```
CALL NIVEL_HABILIDADE('AVANÇADO');
```

| HABILIDADE |
| ---        |
| Excel      |

# Direitos de Uso
***
Este repositório têm como objetivo apresentar o projeto de **currículo** utilizando da linguagem SQL. Então, dentro deste repositório você pode utilizar deste conteúdo sem nenhuma restrição contanto que não me responsebilize por eventuais causas ou danos morais perante minha responsabilidade.	

Exigido | Permitido | Proibido
:---: | :---: | :---:
Aviso de licença e direitos autorais | Uso comercial | Responsabilidade Assegurada
 || Modificação ||	
 || Distribuição ||	
 || Sublicenciamento || 	
