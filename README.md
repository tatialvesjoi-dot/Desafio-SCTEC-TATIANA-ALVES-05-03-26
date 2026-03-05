# Desafio-SCTEC-TATIANA-ALVES-05-03-26
Desafio SCTEC TATIANA ALVES 05-03-26

Descrição da Solução:  DESENVOLVIMENTO DE SERVIÇO (BACK-END)
SQL CRUD Backend Empreendedorismo • Santa Catarina

Ferramenta Utilizada : Software CANVA IA para desenvolver o código na linguagem SQLServer para banco de dados .

PROMPT DE COMANDO :

criar uma sentença sql com as operações de inclusão , alteração , leitura e exclusão de dados para ser utilizado numa aplicação API , do tipo CRUD (Create, Read, Update , Delete ) para levantamento de dados do empreendedorismo no estado de Santa Catarina , contendo os seguintes atributos:Nome do empreendimento; Nome do(a) empreendedor(a) responsável; Município de Santa Catarina; Segmento de atuação,E-mail ou meio de contato; Status (ativo ou inativo).
sendo que o atributo Segmento deve ser do tipo case (escolha) tendo as seguintes opções :Tecnologia; Comércio; Indústria; Serviços; Agronegócio e o atributo Status criar um botão de seleção , utilizando linguagem de banco de dados SQL Server ou que seja compatível com API REST, GraphQL

Resultado:

Funcionalidades incluídas:
Seções visíveis:
CREATE TABLE - Estrutura completa da tabela com índices e constraints
INSERT (CREATE) - Inclusão de novos empreendimentos
SELECT (READ) - 5 variações de consultas (todos, por ID, por segmento, por município, agregação)
UPDATE - 4 variações (atualização completa, campo único, exclusão lógica, verificação)
DELETE - 4 variações (exclusão física, lógica, múltipla, verificação)
STORED PROCEDURES - Procedimentos otimizados prontos para sua API

Código-fonte:  

--1. CREATE TABLE - Estrutura do Banco de Dados

-- SQL Server - Estrutura completa da tabela Empreendimentos_SC

CREATE TABLE Empreendimentos_SC (
    -- Chave primária com auto-incremento
    id_empreendimento INT IDENTITY(1,1) PRIMARY KEY,
    
    -- Dados do empreendimento
    nome_empreendimento NVARCHAR(200) NOT NULL,
    nome_empreendedor NVARCHAR(150) NOT NULL,
    municipio NVARCHAR(100) NOT NULL,
    
    -- Segmento com validação CHECK
    segmento NVARCHAR(50) NOT NULL
        CONSTRAINT CK_Segmento CHECK (
            segmento IN (
                'Tecnologia', 'Comércio', 'Indústria',
                'Serviços', 'Agronegócio'
            )
        ),
    
    -- Contato
    contato NVARCHAR(200) NOT NULL,
    
    -- Status (1 = Ativo, 0 = Inativo)
    status BIT NOT NULL DEFAULT 1,
    
    -- Auditoria de dados
    data_cadastro DATETIME DEFAULT GETDATE(),
    data_atualizacao DATETIME NULL
);
GO

-- Índices para melhor performance
CREATE INDEX IX_Municipio ON Empreendimentos_SC(municipio);
CREATE INDEX IX_Segmento ON Empreendimentos_SC(segmento);
CREATE INDEX IX_Status ON Empreendimentos_SC(status);
GO

--2. INSERT - Inclusão de Novos Registros (CREATE)

-- Inserir novo empreendimento - SQL Server

INSERT INTO Empreendimentos_SC (
    nome_empreendimento,
    nome_empreendedor,
    municipio,
    segmento,
    contato,
    status
)
VALUES (
    'Tech Solutions SC',
    'Maria Silva',
    'Florianópolis',
    'Tecnologia',
    'maria@techsolutions.com.br',
    1  -- 1 = Ativo
);

-- Retorna o ID do novo registro inserido
SELECT SCOPE_IDENTITY() AS novo_id;


--3. SELECT - Leitura de Dados (READ)

-- A) Buscar TODOS os empreendimentos
SELECT
    id_empreendimento,
    nome_empreendimento,
    nome_empreendedor,
    municipio,
    CASE segmento
        WHEN 'Tecnologia' THEN '💻 Tecnologia'
        WHEN 'Comércio' THEN '🛒 Comércio'
        WHEN 'Indústria' THEN '🏭 Indústria'
        WHEN 'Serviços' THEN '🔧 Serviços'
        WHEN 'Agronegócio' THEN '🌾 Agronegócio'
    END AS segmento_formatado,
    contato,
    CASE WHEN status = 1 THEN 'Ativo' ELSE 'Inativo' END AS status_texto,
    data_cadastro
FROM Empreendimentos_SC
WHERE status = 1  -- Apenas ativos
ORDER BY nome_empreendimento ASC;

-- B) Buscar por ID específico
SELECT * FROM Empreendimentos_SC
WHERE id_empreendimento = 1;

-- C) Filtrar por segmento
SELECT * FROM Empreendimentos_SC
WHERE segmento = 'Tecnologia'
ORDER BY nome_empreendimento;

-- D) Filtrar por município
SELECT * FROM Empreendimentos_SC
WHERE municipio = 'Florianópolis';

-- E) Contar registros por segmento
SELECT
    segmento,
    COUNT(*) AS total
FROM Empreendimentos_SC
GROUP BY segmento
ORDER BY total DESC;


--4. UPDATE - Alteração de Dados Existentes

-- A) Atualizar todos os dados de um empreendimento
UPDATE Empreendimentos_SC
SET
    nome_empreendimento = 'Tech Solutions SC - Atualizado',
    nome_empreendedor = 'Maria Silva Santos',
    municipio = 'Joinville',
    segmento = 'Tecnologia',
    contato = 'novo@techsolutions.com.br',
    status = 1,
    data_atualizacao = GETDATE()
WHERE id_empreendimento = 1;

-- B) Atualizar apenas o contato
UPDATE Empreendimentos_SC
SET
    contato = 'email@novodominio.com.br',
    data_atualizacao = GETDATE()
WHERE id_empreendimento = 1;

-- C) Inativar um empreendimento (exclusão lógica)
UPDATE Empreendimentos_SC
SET
    status = 0,
    data_atualizacao = GETDATE()
WHERE id_empreendimento = 1;

-- D) Verificar a alteração
SELECT * FROM Empreendimentos_SC
WHERE id_empreendimento = 1;


--5. DELETE - Exclusão de Registros

-- A) Exclusão FÍSICA (remove permanentemente - cuidado!)
DELETE FROM Empreendimentos_SC
WHERE id_empreendimento = 1;

-- B) Exclusão LÓGICA (RECOMENDADO - apenas marca como inativo)
UPDATE Empreendimentos_SC
SET
    status = 0,
    data_atualizacao = GETDATE()
WHERE id_empreendimento = 1;

-- C) Deletar múltiplos registros por critério
DELETE FROM Empreendimentos_SC
WHERE municipio = 'Outro município' AND status = 0;

-- D) Verificar se o registro foi deletado
SELECT COUNT(*) AS registros_restantes
FROM Empreendimentos_SC
WHERE id_empreendimento = 1;


--6. STORED PROCEDURES - Operações Otimizadas

-- SP: Inserir novo empreendimento
CREATE PROCEDURE sp_InserirEmpreendimento
    @nome_empreendimento NVARCHAR(200),
    @nome_empreendedor NVARCHAR(150),
    @municipio NVARCHAR(100),
    @segmento NVARCHAR(50),
    @contato NVARCHAR(200)
AS
BEGIN
    INSERT INTO Empreendimentos_SC (nome_empreendimento, nome_empreendedor, municipio, segmento, contato)
    VALUES (@nome_empreendimento, @nome_empreendedor, @municipio, @segmento, @contato);
    SELECT SCOPE_IDENTITY() AS novo_id;
END;
GO

-- Executar SP
EXEC sp_InserirEmpreendimento 
    @nome_empreendimento = 'Tech Solutions',
    @nome_empreendedor = 'Maria Silva',
    @municipio = 'Florianópolis',
    @segmento = 'Tecnologia',
    @contato = 'maria@tech.com.br';


-- Endpoints REST API (para integração backend)

POST /api/empreendimentos
/*Cria novo empreendimento (executa INSERT)*/

GET /api/empreendimentos/{id}
/*Busca empreendimento específico (SELECT WHERE)*/

DELETE /api/empreendimentos/{id}
/*Deleta empreendimento (DELETE)*/

GET /api/empreendimentos
/*Busca todos os empreendimentos (SELECT)*/

PUT /api/empreendimentos/{id}
/*Atualiza empreendimento (UPDATE)*/

GET /api/empreendimentos/segmento/{seg}
/*Busca por segmento (SELECT WHERE)*/


LINK PARA O VÍDEO DO PITCH (3 MIN):





