# Mapeamento de Telas e Fluxos (UI/UX)

Este documento centraliza todas as interfaces que precisam ser prototipadas para a plataforma.

## Histórico de Revisões
| Data       | Versão | Descrição das Alterações                               | Autor(es)       |
| :--------- | :----- | :----------------------------------------------------- | :-------------- |
| 02/10/2026 | 1.0    | Mapeamento inicial das telas de Autenticação e Onboarding     | Felipe Postigo  |

## 1. Autenticação e Onboarding

### 1.1 Fluxo de Autenticação (Acesso e Recuperação)

#### 1.1.1 Tela de Login
- **Objetivo:** Permitir o acesso de usuários já cadastrados (Alunos e Instrutores).
- **Elementos Principais:**
    - Campo: Email
    - Campo: Senha (com toggle para ocultar/mostrar)
    - Botão: Entrar
    - Link: "Esqueci minha senha"
    - Botão/Link: "Ainda não tem conta? Cadsastre-se"
- **Rastreabilidade:** US002, RF001

#### 1.1.2 Tela de Esqueci Minha Senha
- **Objetivo:** Iniciar o fluxo de recuperação de acesso.
- **Elementos Principais:**
    - Campo: Email cadastrado
    - Botão: Enviar link de recuperação
    - Link: Voltar para o login
- **Rastreabilidade:** US003, RF001

### 1.2 Fluxo de Cadastro (Onboarding Geral)

#### 1.2.1 Tela de Escolha de Perfil
- **Objetivo:** Direcionar o usuário para o fluxo de cadastro correto.
- **Elementos Principais:**
    - Card/Botão: "Quero aprender a dirigir (Sou Aluno)"
    - Card/Botão: "Quero dar aulas (Sou Instrutor)"

### 1.3 Fluxo de Onboarding - Aluno

#### 1.3.1 Tela de Dados Pessoais (Aluno)
- **Objetivo:** Coletar as informações básicas do aluno.
- **Elementos Principais:**
    - Campos: Nome Completo, CPF, Email, Celular, Senha, Confirmar Senha.
    - Checkbox: "Aceito os Termos de Uso e Política de Privacidade"
    - Botão: Criar Conta
- **Rastreabilidade:** US001

### 1.4 Fluxo de Onboarding - Instrutor

#### 1.4.1 Tela de Dados Pessoais e Profissionais (Instrutor)
- **Objetivo:** Coletar dados de contato e informações de trabalho do instrutor.
- **Elementos Principais:**
    - Campos Básicos: Nome Completo, CPF, Email, Celular, Senha, Confirmar Senha.
    - Campos Profissionais: Valor da Hora-aula (R$), Categorias Atendidas (A, B).
    - Botão: Avançar para Dados do Veículo
- **Rastreabilidade:** US001, US027

#### 1.4.2 Tela de Dados do Veículo
- **Objetivo:** Cadastrar o carro ou moto que será utilizado nas aulas.
- **Elementos Principais:** 
    - Campos: Modelo, Ano, Placa.
    - Seleção: Tipo de Câmbio (Manual ou Automático).
    - Botão: Avançar para Documentação
- **Rastreabilidade:** US025, RF002

#### 1.4.3 Tela de Upload de Documentos
- **Objetivo:** Coletar as imagens para validação no sistema e OCR.
- **Elementos Principais:**
    - Área de Upload 1: Foto da CNH
    - Área de Upload 2: Foto da Credencial do Detran
    - Área de Upload 3: Foto de Perfil
    - Botão: Enviar para Análise
- **Rastreabilidade:** RF003, RF009, RF010

#### 1.4.4 Tela de Loading / Status de Processamento
- **Objetivo:** Informar que os documentos estão sendo analisados e impedir que o usuário fique travado.
- **Elementos Principais:**
    - Animação de Carregamento (Spinner ou Lottie).
    - Texto de Status: "Analisando seus documentos".
    - Feedback: "Você receberá uma notificação assim que a análise for concluída."
    - Botão: "Ir para a Tela Inicial" (Acesso restrito até aprovação)
- **Rastreabilidade:** RF008, RF012, RF013

*Documento criado para basear a construção de Wireframes (Baixa Fidelidade) e UI (Alta Fidelidade).*