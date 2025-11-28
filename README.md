# MedicLifeBD
Banco de Dados da Empresa Medic Life 
-- ==========================================================
-- PROJETO: SISTEMA DE GESTÃO DE CIRURGIAS
-- AUTOR: Fulano de Tal (Aluno de Banco de Dados)
-- DATA: 27/11/2025
-- TIPO: Script Completo (DDL + DML)
-- ==========================================================

-- 1. CRIAÇÃO DO BANCO DE DADOS
CREATE DATABASE IF NOT EXISTS db_clinica_cirurgica;
USE db_clinica_cirurgica;

-- ==========================================================
-- 2. CRIAÇÃO DAS TABELAS (DDL)
-- ==========================================================

-- Tabela: Especialidades
CREATE TABLE Especialidades (
    id_especialidade INT AUTO_INCREMENT PRIMARY KEY,
    nome_especialidade VARCHAR(50) NOT NULL
);

-- Tabela: Pacientes
CREATE TABLE Pacientes (
    id_paciente INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    cpf VARCHAR(14) NOT NULL UNIQUE,
    data_nascimento DATE NOT NULL,
    telefone VARCHAR(20) DEFAULT 'Não informado'
);

-- Tabela: Medicos (Chave Estrangeira -> Especialidades)
CREATE TABLE Medicos (
    id_medico INT AUTO_INCREMENT PRIMARY KEY,
    nome VARCHAR(100) NOT NULL,
    crm VARCHAR(20) NOT NULL UNIQUE,
    id_especialidade INT,
    FOREIGN KEY (id_especialidade) REFERENCES Especialidades(id_especialidade)
);

-- Tabela: Cirurgias (Chave Estrangeira -> Pacientes e Medicos)
CREATE TABLE Cirurgias (
    id_cirurgia INT AUTO_INCREMENT PRIMARY KEY,
    id_paciente INT NOT NULL,
    id_medico INT NOT NULL,
    data_cirurgia DATETIME NOT NULL,
    status_cirurgia VARCHAR(20) DEFAULT 'Agendada',
    observacoes TEXT,
    FOREIGN KEY (id_paciente) REFERENCES Pacientes(id_paciente),
    FOREIGN KEY (id_medico) REFERENCES Medicos(id_medico)
);

-- ==========================================================
-- 3. INSERÇÃO DE DADOS (DML - INSERT)
-- ==========================================================

-- Inserindo Especialidades
INSERT INTO Especialidades (nome_especialidade) VALUES 
('Cardiologia'), 
('Ortopedia'), 
('Neurologia'), 
('Cirurgia Geral'), 
('Pediatria');

-- Inserindo Médicos (Dados Fictícios Completos)
INSERT INTO Medicos (nome, crm, id_especialidade) VALUES 
('Dr. Roberto Silva', 'CRM-RJ-12345', 1),
('Dra. Ana Souza', 'CRM-RJ-67890', 2),
('Dr. Marcos Paulo', 'CRM-RJ-54321', 4),
('Dra. Carla Dias', 'CRM-RJ-98765', 1),
('Dr. Lucas Mendes', 'CRM-RJ-11223', 3);

-- Inserindo Pacientes (Dados Fictícios Completos)
INSERT INTO Pacientes (nome, cpf, data_nascimento, telefone) VALUES 
('João da Silva', '111.222.333-44', '1985-05-20', '(21) 99999-1111'),
('Maria Oliveira', '555.666.777-88', '1990-10-15', '(21) 98888-2222'),
('Carlos Pereira', '999.888.777-66', '1978-03-30', '(21) 97777-3333'),
('Fernanda Costa', '222.333.444-55', '2000-01-10', '(21) 96666-4444'),
('Pedro Santos', '444.555.666-77', '1995-07-25', '(21) 95555-5555');

-- Inserindo Cirurgias (Dados Fictícios Completos)
INSERT INTO Cirurgias (id_paciente, id_medico, data_cirurgia, status_cirurgia, observacoes) VALUES 
(1, 1, '2025-12-10 08:00:00', 'Agendada', 'Procedimento de Ponte de Safena - Requer UTI'),
(2, 2, '2025-12-11 14:00:00', 'Agendada', 'Artroscopia de Joelho Direito - Menisco'),
(3, 3, '2025-11-20 09:00:00', 'Realizada', 'Apendicectomia de urgência sem complicações'),
(4, 1, '2026-01-15 10:00:00', 'Pendente', 'Avaliação cardiológica pendente para liberação'),
(5, 3, '2025-11-25 11:30:00', 'Cancelada', 'Paciente apresentou quadro febril no pré-operatório');

-- ==========================================================
-- 4. CONSULTAS (DML - SELECT)
-- ==========================================================

-- Consulta A: Filtro com WHERE e Ordenação
SELECT * FROM Cirurgias 
WHERE status_cirurgia = 'Agendada' 
ORDER BY data_cirurgia ASC;

-- Consulta B: Junção de tabelas (JOIN) - Relatório Completo
SELECT 
    c.id_cirurgia,
    p.nome AS Nome_Paciente,
    m.nome AS Nome_Medico,
    e.nome_especialidade AS Especialidade,
    c.data_cirurgia,
    c.status_cirurgia
FROM Cirurgias c
INNER JOIN Pacientes p ON c.id_paciente = p.id_paciente
INNER JOIN Medicos m ON c.id_medico = m.id_medico
INNER JOIN Especialidades e ON m.id_especialidade = e.id_especialidade;

-- Consulta C: Limite de registros (Apenas os primeiros 3 pacientes)
SELECT * FROM Pacientes LIMIT 3;

-- ==========================================================
-- 5. ATUALIZAÇÕES (DML - UPDATE)
-- ==========================================================

-- Update 1: Atualizar status da cirurgia id 3 para Concluída
UPDATE Cirurgias 
SET status_cirurgia = 'Concluída', observacoes = 'Alta médica concedida' 
WHERE id_cirurgia = 3;

-- Update 2: Atualizar telefone do paciente id 2
UPDATE Pacientes 
SET telefone = '(21) 90000-9999' 
WHERE id_paciente = 2;

-- Update 3: Corrigir nome do médico id 1
UPDATE Medicos 
SET nome = 'Dr. Roberto da Silva Junior' 
WHERE id_medico = 1;

-- ==========================================================
-- 6. EXCLUSÕES (DML - DELETE)
-- ==========================================================

-- Delete 1: Remover cirurgia que estava Pendente (id 4)
DELETE FROM Cirurgias 
WHERE status_cirurgia = 'Pendente' AND id_paciente = 4;

-- Delete 2: Remover especialidade 'Pediatria' (não tem médicos vinculados)
DELETE FROM Especialidades 
WHERE nome_especialidade = 'Pediatria';

-- Delete 3: Remover médico 'Dra. Carla Dias' (não tem cirurgias vinculadas)
DELETE FROM Medicos 
WHERE crm = 'CRM-RJ-98765';
