/*
identificar aonde estar ocorrendo o locks
*/
SELECT sid, noProcesso, username, command, lockwait, osuser 
FROM v$session 
WHERE lockwait IS NOT NULL;


/*
matando um processo.
altere no sql abaixo o sid pelo SID eo noProcesso pelo numero do Processo 
*/
ALTER SYSTEM KILL SESSION 'sid, noProcesso';


/*
SQL que estar causando o bloqueio
*/
SELECT sql_text FROM v$sqltext 
WHERE (address,hash_value) 
IN
(SELECT sql_address,sql_hash_value FROM v$session WHERE lockwait IS NOT NULL);


/*
	MONTANDO CENARIO -- precisaremos de 3 sessões
	sessao1 = mariazinha
	sessao2 = joaozinho
	sessao3 = o dba
	
	Mariazinha tem que alterar o nome de uma Pessoa no sistema. Sendo que Mariazinha edita e vai ao banheiro ... Não conclui a alteração ( COMMIT)
	Joaozinho recebe uma ligacao reclamando que o nome ainda nao foi alterado , Joaozinho altera o nome e faz outras alterações sem concluir todas (sem dar COMMIT) 
	
	DBA ver se tem DEADLOCKS e acha UM..... 
	
	
*/

--OBS:: Os updates não teram commit, 


-- SESSAO 1
--CRIAR TABELA E POPULAR
CREATE TABLE Person (id number NOT NULL, name varchar2(100) NOT NULL);

INSERT INTO Person (id, name) VALUES (1,'Marcos');

INSERT INTO Person (id, name) VALUES (2,'Taiana');

INSERT INTO Person (id, name) VALUES (3,'Jesus');

COMMIT;


--SESSAO 1
-- AGORA IREMOS FAZER UM UPDATE 
UPDATE Person SET name="Marcos Felipe" WHERE id=1;


--SESSAO 2
--VAMOS ALTERAR O NOME 
UPDATE Person SET name="Marcos Felipe" WHERE id=3;

--SESSAO 3
--Você é o DBA, verifique se tem DEADLOCKs, ache o sql desse DEADLOCKS , mata o processo e faça o sql desse DEADLOCKS. 
-- Muito bem , você é um grande DBA =)





