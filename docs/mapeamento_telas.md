# Mapeamento de Telas e Fluxos (UI/UX)

Este documento centraliza todas as interfaces que precisam ser prototipadas para a plataforma.

## Histórico de Revisões
| Data       | Versão | Descrição das Alterações                               | Autor(es)       |
| :--------- | :----- | :----------------------------------------------------- | :-------------- |
| 02/10/2026 | 1.0    | Mapeamento inicial das telas de Autenticação e Onboarding     | Felipe Postigo  |
| 03/10/2026 | 1.1    | Mapeamento inicial das telas de Busca e Agendamento| Felipe Postigo  |

## 1. Autenticação e Onboarding

### 1.1. Fluxo de Autenticação (Acesso e Recuperação)

#### 1.1.1. Tela de Login
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

### 1.3. Fluxo de Onboarding - Aluno

#### 1.3.1. Tela de Dados Pessoais (Aluno)
- **Objetivo:** Coletar as informações básicas do aluno.
- **Elementos Principais:**
    - Campos: Nome Completo, CPF, Email, Celular, Senha, Confirmar Senha.
    - Checkbox: "Aceito os Termos de Uso e Política de Privacidade"
    - Botão: Criar Conta
- **Rastreabilidade:** US001

### 1.4. Fluxo de Onboarding - Instrutor

#### 1.4.1. Tela de Dados Pessoais e Profissionais (Instrutor)
- **Objetivo:** Coletar dados de contato e informações de trabalho do instrutor.
- **Elementos Principais:**
    - Campos Básicos: Nome Completo, CPF, Email, Celular, Senha, Confirmar Senha.
    - Campos Profissionais: Valor da Hora-aula (R$), Categorias Atendidas (A, B).
    - Botão: Avançar para Dados do Veículo
- **Rastreabilidade:** US001, US027

#### 1.4.2. Tela de Dados do Veículo
- **Objetivo:** Cadastrar o carro ou moto que será utilizado nas aulas.
- **Elementos Principais:** 
    - Campos: Modelo, Ano, Placa.
    - Seleção: Tipo de Câmbio (Manual ou Automático).
    - Botão: Avançar para Documentação
- **Rastreabilidade:** US025, RF002

#### 1.4.3. Tela de Upload de Documentos
- **Objetivo:** Coletar as imagens para validação no sistema e OCR.
- **Elementos Principais:**
    - Área de Upload 1: Foto da CNH
    - Área de Upload 2: Foto da Credencial do Detran
    - Área de Upload 3: Foto de Perfil
    - Botão: Enviar para Análise
- **Rastreabilidade:** RF003, RF009, RF010

#### 1.4.4. Tela de Loading / Status de Processamento
- **Objetivo:** Informar que os documentos estão sendo analisados e impedir que o usuário fique travado.
- **Elementos Principais:**
    - Animação de Carregamento (Spinner ou Lottie).
    - Texto de Status: "Analisando seus documentos".
    - Feedback: "Você receberá uma notificação assim que a análise for concluída."
    - Botão: "Ir para a Tela Inicial" (Acesso restrito até aprovação)
- **Rastreabilidade:** RF008, RF012, RF013

## 2. Épico: Busca e Agendamento

### 2.1. Fluxo de Busca e Filtros

#### 2.1.1. Tela Principal (Mapa e Lista)
* **Objetivo:** Criar a interface para o aluno localizar instrutores próximos em um raio de 5km.
* **Elementos Principais:**
  * Alternância de visualização: Mapa ou Lista de instrutores.
  * Barra superior para digitação de endereço ou uso de GPS.
  * Pins no mapa representando os instrutores ativos em um raio de 5km.
  * Botão flutuante ou barra para abrir os Filtros.
* **Rastreabilidade:** US006, US012, RF014, RF015, RF039.

#### 2.1.2. Componente de Filtros
* **Objetivo:** Permitir que o aluno aplique filtros para refinar a busca.
* **Elementos Principais:**
  * Seleção de Câmbio: Manual ou Automático.
  * Seleção de Categoria da CNH: Moto ou Carro.
  * Filtro de Preço da hora-aula.
  * Ordenação por avaliação.
  * Botões: "Aplicar Filtros" e "Limpar".
* **Rastreabilidade:** US008, US009, US013, US020.

### 2.2. Fluxo de Seleção e Agendamento

#### 2.2.1. Perfil Público do Instrutor
* **Objetivo:** Visualizar o perfil do profissional selecionado e escolher um horário.
* **Elementos Principais:**
  * Cabeçalho: Foto, Nome e Veículo (Modelo/Ano/Placa).
  * Indicadores: Média de notas (avaliação).
  * Informação Financeira: Formas de pagamento aceitas e valor da hora.
  * Interação: Grade de horários disponíveis para seleção.
  * Seleção: Quantidade de horas desejadas para a aula.
  * Botão: Avançar para Agendamento.
* **Rastreabilidade:** US007, US011, US021, RF038.

#### 2.2.2. Tela de Revisão do Agendamento
* **Objetivo:** Exibir o resumo do pedido e confirmar o agendamento da aula.
* **Elementos Principais:**
  * Resumo dos Dados: Data, horário de início, duração e endereço de referência.
  * Cálculo: Valor total estimado da aula.
  * Aviso: "Disclaimer Legal" informando que o pagamento deve ser feito diretamente ao instrutor no momento do encontro.
  * Botão: Confirmar Agendamento.
* **Rastreabilidade:** US010, US017, US023, RF017, RF025.

*Documento criado para basear a construção de Wireframes (Baixa Fidelidade) e UI (Alta Fidelidade).*