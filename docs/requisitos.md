# Documento de Requisitos - Marketplace de Autoescola 

## Histórico de Revisões
| Data       | Versão | Descrição das Alterações                                                                 | Autor(es)       |
| :--------- | :----- | :--------------------------------------------------------------------------------------- | :-------------- |
| 16/08/2026 | 1.0    | Criação do documento com requisitos funcionais, não funcionais, atores fluxos lógicos, validação OCR e Dashboard.       | Felipe Postigo  |

---

## 1. Visão Geral do Produto
*Uma plataforma que busca conectar alunos que estão tirando sua primeira habilitação e instrutores autônomos*

---

## 2. Regras de Negócio e Fluxo

### Fluxo de Validação de Segurança (Onboarding do Instrutor)
1. O instrutor cria a conta e faz o upload da foto da CNH e da Credencial do Detran.
2. O sistema salva o status do instrutor como `Em Processamento`.
3. O back-end envia as imagens para a API de OCR para extração do texto.
4. O sistema analisa o texto retornado buscando padrões e palavras-chave obrigatórias (ex: "DETRAN", "PR", "INSTRUTOR DE TRÂNSITO", "VALIDADE", e confere se o nome bate com o cadastro).
5. **Se a API de OCR retornar um Nível de Confiança (`Score_Confianca_OCR`) igual ou superior a 85% para as palavras-chave obrigatórias e as datas forem válidas:** O status do instrutor muda automaticamente para `Ativo` e ele passa a aparecer nas buscas.
6. **Se o sistema não encontrar as palavras (foto borrada, documento incorreto) ou a validade expirar:** O status muda para `Pendente (Revisão Manual)`, e o perfil entra na fila do painel do Administrador (Backoffice) para aprovação ou reprovação humana.

### Fluxo de Busca e Agendamento de Aula
1. O Aluno acessa a plataforma e compartilha sua localização atual (ou digita um endereço).
2. O sistema exibe uma lista apenas com Instrutores com status `Ativo` em um raio de distância de 5km.
3. O Aluno aplica filtros (ex: carro manual/automático, preço, categoria de CNH).
4. O Aluno acessa o perfil do Instrutor, visualiza a grade de horários disponíveis e seleciona a data, horário de início e a duração da aula.
5. O sistema cria o agendamento com o status `Pendente de Confirmacao` e bloqueia temporariamente aquele horário na agenda do Instrutor. O ponto exato de encontro (ex: "em frente à padaria") será alinhado via chat após a confirmação.
6. O Instrutor recebe uma notificação (push ou e-mail) sobre o pedido de aula.
7. Se o Instrutor **Aceitar**, o status muda para `Confirmada` e o Aluno é notificado. O horário fica definitivamente bloqueado na agenda.
8. Se o Instrutor **Recusar** (ou não responder em 12 horas), o status muda para `Cancelada`, o horário é liberado na agenda e o Aluno é notificado para buscar outro profissional.

### Fluxo de Avaliação e Reviews
1. A avaliação só é permitida para aulas cujo status na máquina de estados atingiu `Concluida`.
2. Após a conclusão, o sistema exibe um modal no app pedindo para o Aluno avaliar o Instrutor.
3. O Aluno seleciona uma nota obrigatória de 1 a 5 estrelas e pode, opcionalmente, deixar um comentário em texto.
4. Assim que a avaliação é salva, o sistema recalcula a média geral do Instrutor (soma de todas as notas dividida pelo total de avaliações).
5. A nova média e o comentário passam a ficar visíveis no perfil público do Instrutor para outros alunos verem.
6. Regra Antifraude: Um aluno só pode avaliar uma aula específica uma única vez.

### Fluxo de Cancelamento
1. O Aluno ou o Instrutor clica no botão "Cancelar Aula" nos detalhes do agendamento.
2. O sistema verifica quem é o autor do cancelamento e a diferença de tempo entre o momento atual e a `Hora_Inicio` da aula.
3. **Se o Autor for o Instrutor ou o Aluno:** O status da aula muda para `Cancelada`, o horário é liberado na agenda.
4. **Se o aluno não comparecer:** O Instrutor muda o status para `Cancelada_No_Show`.
5. Em todos os casos, a parte que sofreu o cancelamento recebe uma notificação (push/e-mail) informando o ocorrido.

### Fluxo de Punições (No-Show e Cancelamento Tardio)
1. É considerado "Cancelamento Tardio" qualquer cancelamento feito com menos de 2 horas de antecedência do início da aula.
2. É considerado "No-Show" quando a aula não ocorre porque uma das partes não compareceu ao local de encontro após 15 minutos de tolerância.

3. Se um Aluno acumular 2 (duas) ocorrências (somando No-Shows e/ou Cancelamentos Tardios), o sistema bloqueia automaticamente a conta dele por 7 dias para novos agendamentos.

4. Se um Instrutor acumular 2 (duas) ocorrências, o perfil dele tem o status alterado para Bloqueado e vai para a fila do Administrador, pois o cancelamento por parte do instrutor prejudica gravemente a confiabilidade da plataforma.

---

## 3. Atores do Sistema
* **Usuário:** Instrutores, alunos e administradores que usam o sistema.
* **Aluno:** Usuário que busca instruções e agenda aulas.
* **Instrutor:** Profissional autônomo que oferta seus serviços. 
* **Administrador:** Pessoa que gerencia a plataforma.

---

## 4. Histórias de Usuário

### Painel do Usuário
* **US001:** Como usuário (aluno ou instrutor), eu quero poder criar uma conta utilizando meu e-mail e senha, para ter acesso às funcionalidades da plataforma.
* **US002:** Como usuário, eu quero fazer login no aplicativo utilizando minhas credenciais, para acessar meu painel de forma segura.
* **US003:** Como usuário, caso eu esqueça minha senha, eu quero poder solicitar um link ou código de recuperação via e-mail, para redefinir meu acesso.
* **US004:** Como usuário, eu quero poder fazer logout da minha conta, para proteger meus dados caso eu compartilhe o dispositivo com outra pessoa.
* **US005:** Como usuário, eu quero ter a opção de "Excluir Minha Conta" nas configurações do aplicativo, para garantir meu direito à privacidade (LGPD) e remover meus dados da plataforma.

### Painel do Aluno
* **US006:** Como aluno, eu quero buscar instrutores por localidade para encontrar o mais próximo de mim.
* **US007:** Como aluno, eu quero ver as avaliações do instrutor para me sentir mais seguro antes de conectar.
* **US008:** Como aluno, eu quero buscar instrutores por avaliação para me sentir mais seguro.
* **US009:** Como aluno, eu quero buscar instrutores por preço para saber quais cabem no meu orçamento.
* **US010:** Como aluno, eu quero agendar um horário disponível na agenda do instrutor.
* **US011:** Como aluno, eu quero escolher a quantidade de horas para a minha aula.
* **US012:** Como aluno, eu quero visualizar os instrutores em um mapa ou lista baseada na minha localização (GPS), para encontrar os mais próximos.
* **US013:** Como aluno, eu quero filtrar os instrutores pelo tipo de câmbio do veículo (Manual ou Automático), para treinar no carro adequado à minha necessidade.
* **US014:** Como aluno, eu quero acompanhar o status do meu pedido de aula (Pendente, Confirmada, Concluída ou Cancelada) para saber se o instrutor aceitou me atender.
* **US015:** Como aluno, eu quero poder avaliar o instrutor com 1 a 5 estrelas após a conclusão da aula, para compartilhar minha experiência.
* **US016:** Como aluno, eu quero poder escrever um comentário opcional na minha avaliação, para detalhar o que gostei ou não na didática do instrutor.
* **US017:** Como aluno, eu quero visualizar o valor total estimado da aula antes de confirmar o agendamento, para saber quanto deverei pagar diretamente ao instrutor.
* **US018:** Como aluno, eu quero poder cancelar uma aula agendada pelo aplicativo, para desistir do serviço caso ocorra um imprevisto.
* **US019:** Como aluno, eu quero que o valor da minha aula agendada permaneça o mesmo até a conclusão do serviço, mesmo que o instrutor altere o preço no perfil dele dias depois.
* **US020:** Como aluno, eu quero buscar instrutores pela categoria da minha CNH (Moto ou Carro).
* **US021:** Como aluno, eu quero visualizar as formas de pagamento aceitas pelo instrutor (Ex: Pix, Dinheiro, Cartão) no perfil dele, para saber se tenho como pagá-lo no momento do encontro. 
* **US022:** Como aluno, eu quero me comunicar com o instrutor através de um chat seguro dentro do aplicativo após a confirmação da aula, para alinhar o local exato do encontro sem precisar expor meu número de telefone pessoal.
* **US023:** Como aluno, eu quero que o endereço que utilizei na busca seja enviado como referência no pedido de aula, para que o instrutor saiba em qual região ele precisará me atender.
* **US024:** Como aluno, eu quero visualizar um PIN de segurança de 4 dígitos na tela da minha aula confirmada, para informá-lo ao instrutor quando nos encontrarmos.

### Painel do Instrutor
* **US025:** Como instrutor, eu quero cadastrar os dados do meu veículo (modelo, ano, placa) para que o aluno saiba em qual carro vai fazer a aula.
* **US026:** Como instrutor, eu quero definir horários na minha agenda para que eu possa controlar as minhas aulas.
* **US027:** Como instrutor, eu quero cadastrar o valor da minha hora para que o aluno veja quanto ele vai pagar.
* **US028:** Como instrutor, eu quero visualizar o status de aprovação do meu perfil (Pendente, Aprovado ou Reprovado) na tela inicial do meu aplicativo, para saber se já posso receber alunos.
* **US029:** Como instrutor, eu quero receber um alerta quando um aluno solicitar uma aula, para que eu possa responder rapidamente.
* **US030:** Como instrutor, eu quero poder "Aceitar" ou "Recusar" uma solicitação de aula, para ter controle sobre a minha disponibilidade real e segurança.
* **US031:** Como instrutor, eu quero visualizar a minha agenda com as aulas confirmadas, para organizar o meu dia de trabalho.
* **US032:** Como instrutor, eu quero visualizar no meu painel a minha nota média e ler os comentários recebidos, para entender como posso melhorar meu atendimento.
* **US033:** Como instrutor, eu quero poder cancelar uma aula confirmada caso eu tenha um problema com o veículo ou um imprevisto grave.
* **US034:** Como instrutor, eu quero poder sinalizar que o aluno não compareceu ao local (No-Show) após a tolerância de 15 minutos.
* **US035:** Como instrutor, eu quero enviar e receber mensagens do aluno pelo chat da plataforma, para avisar sobre imprevistos no trânsito ou confirmar características do aluno no local de encontro.
* **US036:** Como instrutor, eu quero visualizar o endereço de referência do aluno antes de aceitar a aula, para calcular meu tempo de deslocamento até a região solicitada.
* **US037:** Como instrutor, eu quero que o sistema me exija digitar o PIN fornecido pelo aluno para alterar o status da aula de `Confirmada` para `Em_Andamento`, garantindo que o encontro realmente aconteceu.

### Painel do Administrador
* **US038:** Como administrador, eu quero visualizar em tela cheia as fotos enviadas pelo instrutor (CNH, Credencial Detran, Documento do Veículo) para verificar a autenticidade e validade.
* **US039:** Como administrador, eu quero ter a opção de "Aprovar", "Reprovar" ou "Bloquear" um cadastro para controlar o acesso de profissionais à plataforma.
* **US040:** Como administrador, ao "Reprovar" ou "Bloquear" um instrutor, eu quero poder digitar o motivo da reprovação, para que o instrutor saiba o que precisa corrigir.
* **US041:** Como administrador, eu quero poder zerar manualmente o contador de infrações de um usuário ou remover uma suspensão, para corrigir bloqueios injustos após analisar as mensagens do chat.
* **US042:** Como administrador, eu quero visualizar um dashboard na tela inicial do meu painel contendo o total de alunos, o total de instrutores e o volume de aulas, para acompanhar o crescimento e a saúde do negócio em tempo real.

---

## 5. Requisitos Funcionais (RF)

* **RF001:** O sistema deve possuir um módulo completo de autenticação JWT (ou similar), permitindo Cadastro, Login, Logout e Recuperação de Senha via envio de token para o e-mail cadastrado.
* **RF002:** O sistema deve permitir o cadastro de veículos com ano, cor, placa e documento.
* **RF003:** O sistema deve permitir o cadastro de motorista com CNH.
* **RF004:** O sistema deve verificar se o motorista está habilitado para ser um instrutor.
* **RF005:** O sistema deve ter níveis de acessos diferentes para Instrutores e Alunos.
* **RF006:** O sistema deve enviar um email de confirmação da aula para o Instrutor.
* **RF007:** O sistema deve enviar um email de confirmação da aula para o Aluno.
* **RF008:** O sistema deve calcular o valor total da aula com base no valor da hora do instrutor.
* **RF009:** O sistema deve integrar-se com uma API de OCR para extrair o texto das imagens enviadas (CNH e Credencial) pelo instrutor.
* **RF010:** O sistema deve validar a presença de palavras-chave obrigatórias no texto extraído pelo OCR e extrair os dados estruturados (Nome, CPF, Data de Validade e Categoria da CNH).
* **RF011:** O sistema deve comparar se o Nome e o CPF lidos pelo OCR na imagem correspondem aos dados digitados no cadastro.
* **RF012:** O sistema deve alterar o status do instrutor automaticamente para "Ativo" se o `Score_Confianca_OCR` for igual ou superior a 85% e os dados críticos (Nome, CPF e Data de Validade) forem extraídos e validados com sucesso.
* **RF013:** O sistema deve alterar o status do instrutor para "Pendente (Revisão Manual)" caso o `Score_Confianca_OCR` seja inferior a 85%, o documento esteja ilegível, a validade esteja expirada ou haja divergência entre o texto extraído e os dados digitados no cadastro.
* **RF014:** O sistema não deve exibir perfis com status "Em Processamento", "Pendente" ou "Reprovado" nos resultados de busca do Aluno.
* **RF015:** O sistema deve integrar-se com uma API de mapas/geolocalização (ex: Google Maps ou Mapbox) para calcular a distância entre o Aluno e os Instrutores.
* **RF016:** O sistema deve permitir o cadastro da disponibilidade do Instrutor (dias da semana e janelas de horário, ex: Seg a Sex, das 08h às 18h).
* **RF017:** O sistema não deve permitir que um Aluno solicite um horário que já esteja ocupado por uma aula "Confirmada" ou "Pendente de Confirmação" na agenda do Instrutor.
* **RF018:** O sistema deve alterar automaticamente o status de uma solicitação "Pendente de Confirmação" para "Cancelada" caso o instrutor não responda em até 12 horas.
* **RF019:** O sistema deve gerenciar os status da Aula seguindo a máquina de estados: `Pendente_Confirmacao -> Confirmada -> Em_Andamento -> Concluida ou Cancelada`.
* **RF020:** O sistema deve liberar o formulário de avaliação apenas para aulas que possuam o status `Concluida`.
* **RF021:** O sistema deve exigir uma nota numérica inteira de 1 a 5 no momento da avaliação.
* **RF022:** O sistema deve permitir a inclusão de um texto (comentário) opcional com limite de caracteres (ex: 500 caracteres).
* **RF023:** O sistema deve recalcular automaticamente a Media_Avaliacoes do Instrutor sempre que uma nova nota for inserida.
* **RF024:** O sistema deve impedir que uma mesma aula (ID_Aula) receba mais de uma avaliação do aluno.
* **RF025:** O sistema deve exibir um aviso legal (Disclaimer) na tela final de agendamento informando que o pagamento deve ser realizado diretamente ao instrutor no momento do encontro.
* **RF026:** O sistema deve liberar exclusivamente um chat interno quando a aula atingir o status Confirmada, para que aluno e instrutor possam combinar detalhes do encontro e do pagamento, não permitindo links de redirecionamento para aplicativos externos.
* **RF027:** O sistema deve exigir que o instrutor digite o "PIN de Segurança" (visível apenas no app do aluno) para alterar o status da aula de `Confirmada` para `Em_Andamento`. A alteração de `Em_Andamento` para `Concluida` poderá ser feita de forma unilateral pelo instrutor ao final do horário.
* **RF037:** O sistema de chat interno deve possuir um filtro (ex: Regex) que detecta e oculta automaticamente padrões de números de telefone ou links nas mensagens, exibindo um aviso de que a comunicação deve ser mantida na plataforma por motivos de segurança.
* **RF028:** O sistema deve converter a "Data de Validade" lida pelo OCR em formato de data válida e reprovar automaticamente (jogar para Revisão Manual) qualquer documento cuja validade seja anterior à data atual.
* **RF029:** O sistema deve cruzar a "Categoria da CNH" lida pelo OCR com as categorias cadastradas no perfil do instrutor. Se ele ofertar aulas para categorias que não possui na CNH, o perfil deve ir para Revisão Manual.
* **RF030:** O sistema deve calcular automaticamente a diferença de horas entre o Timestamp do pedido de cancelamento e o Timestamp do início da aula (Data_Aula + Hora_Inicio).
* **RF031:** O sistema deve permitir que o status da aula seja alterado para `Cancelada_No_Show` pelo Instrutor apenas dentro do período de horário em que a aula deveria ocorrer.
* **RF032:** O sistema deve registrar o "Autor do Cancelamento" (Aluno, Instrutor ou Sistema) e o "Motivo" toda vez que uma aula for cancelada, para fins de auditoria e resolução de disputas.
* **RF033:** No momento em que o Aluno inicia o agendamento (status `Pendente_Confirmacao`), o sistema deve realizar um Snapshot (cópia estática) do valor da hora atual do Instrutor, salvando-o na tabela `Agendamento_Aula`.
* **RF034:** Todos os cálculos de `Valor_Total` devem ser realizados usando exclusivamente os campos do Snapshot salvos no Agendamento, ignorando os valores atuais do perfil do Instrutor ou do sistema.
* **RF035:** Se um Instrutor alterar o "Valor por hora" em seu perfil, a mudança só deve ser refletida em solicitações de agendamentos criadas após a atualização.
* **RF036:** O sistema deve alterar automaticamente o status da aula de `Confirmada` ou `Em_Andamento` para `Concluida` após 24 horas do horário de término previsto (`Hora_Fim`), caso o instrutor não faça a atualização manual.
* **RF038:** O sistema deve exibir o array Formas_Pagamento_Aceitas na tela de perfil do Instrutor e na tela de revisão do Agendamento.
* **RF039:** O sistema deve capturar a localização atual do GPS ou o endereço digitado pelo aluno no momento da busca e salvá-lo no agendamento como "Endereço de Referência", deixando a definição do ponto exato de encontro a cargo da negociação via chat interno entre aluno e instrutor.
* **RF041:** O sistema deve classificar automaticamente como "Cancelamento Tardio" se o Timestamp do cancelamento for menor que 2 horas em relação à Hora_Inicio da aula agendada.
* **RF042:** O sistema deve incrementar automaticamente um contador no perfil do usuário (Aluno ou Instrutor) toda vez que ele for o "Autor" de um Cancelamento Tardio ou receber um status de Cancelada_No_Show da outra parte.
* **RF043:** O sistema deve suspender temporariamente o Aluno por 7 dias consecutivos, impedindo a criação de novos pedidos de aula, assim que seu contador atingir 2 ocorrências.
* **RF044:** O sistema deve alterar o status da conta do Instrutor para Bloqueado e disparar um alerta no painel do Administrador caso o contador do Instrutor atinja 2 ocorrências.
* **RF045:** O sistema deve zerar o contador de infrações de um usuário automaticamente após 30 dias da última ocorrência registrada, caso ele não tenha atingido o limite de bloqueio.
* **RF046:** O sistema deve disponibilizar um botão de "Excluir Conta" na área de perfil do aplicativo (Aluno e Instrutor). Ao acionado, o sistema deve inativar imediatamente o acesso do usuário, agendar a deleção/anonimização dos dados no banco de dados e interromper o envio de comunicações, cumprindo as exigências das lojas de aplicativos e da LGPD.
* **RF047:** O sistema deve exibir no dashboard do Administrador os seguintes indicadores consolidados em tempo real:
    * **Total de Alunos:** Contagem total de cadastros na tabela `Aluno`.
    * **Funil de Instrutores:** Contagem na tabela Instrutor agrupada por status (`Em Processamento`, `Pendente`, `Ativo`, `Reprovado`, `Bloqueado`), para facilitar a gestão da fila de aprovação.
    * **Volume de Aulas:** Contagem na tabela `Agendamento_Aula` agrupada por status (com foco em `Concluida`, `Cancelada` e `Cancelada_No_Show`), permitindo filtrar por um período de tempo (ex: últimos 30 dias).

---

## 6. Requisitos Não Funcionais (RNF)

* **RNF001:** As senhas dos usuários devem ser criptografadas antes de serem salvas no banco de dados.
* **RNF002:** A busca por instrutores deve retornar resultados em menos de 2 segundos.
* **RNF003:** O sistema deve ser totalmente responsivo.
* **RNF004:** O aplicativo deve comprimir a imagem da CNH/Credencial e garantir que ela tenha um tamanho máximo (ex: 5MB) antes de enviar para a API de OCR, visando economia de banda e custos da API.
* **RNF005:** Todo o tráfego de dados sensíveis (dados pessoais, imagens de documentos e mensagens do chat) deve ser obrigatoriamente criptografado em trânsito utilizando o protocolo HTTPS/TLS 1.2 ou superior.
* **RNF006:** As imagens dos documentos (CNH e Credencial) enviadas para validação via OCR devem ser armazenadas em diretórios em nuvem (ex: S3 Buckets) com acesso privado e restrito.
* **RNF007:** O sistema deve possuir uma rotina (automatizada ou manual via banco de dados) de deleção definitiva (Direito ao Esquecimento - LGPD) de todos os dados sensíveis e anonimização de histórico de aulas caso um usuário solicite a exclusão de sua conta.
* **RNF008:** A comunicação com a API de OCR deve ser implementada de forma assíncrona (background processing ou webhooks). Após o envio das fotos, o aplicativo não deve bloquear a navegação do usuário; ele deve ser redirecionado para a tela inicial com o status `Em Processamento` e ser notificado via Push Notification ou E-mail assim que a validação for finalizada.
* **RNF009:** O banco de dados deve manter um log imutável de alterações críticas, registrando o Timestamp e o ID do Autor sempre que houver mudanças no status de um agendamento de aula ou no status de aprovação do perfil de um instrutor.

---

## 7. Dicionário de Dados

**Instrutor:**
* ID (UID - Chave Primária)
* Nome Completo (String)
* CPF (String, único)
* Email (String)
* Telefone (String)
* Status Conta (Enum: "Em Processamento", "Ativo", "Pendente", "Reprovado", "Bloqueado")
* CNH (String)
* Credencial do DETRAN (String)
* Foto de Perfil (Imagem)
* Log_OCR_CNH (JSON)
* Log_OCR_Credencial (JSON)
* Score_Confianca_OCR (Float)
* Valor por hora (Float)
* Avaliação (Float)
* Total_Avaliacoes (Integer)
* Categoria_CNH_Atendida (Array de String: "A", "B")
* Formas_Pagamento_Aceitas (Array de String: ex. ["Dinheiro", "Pix", "Cartão de Débito (Maquininha)"])
* Contador_Infracoes (Integer, default 0)

**Aluno:**
* ID (UID - Chave Primária)
* Nome Completo (String)
* CPF (String, único)
* Email (String)
* Telefone (String)
* Categoria_CNH_Desejada (Array de String: "A", "B")
* Contador_Infracoes (Integer, default 0)
* Data_Fim_Suspensao (Timestamp, nullable)

**Veículo:**
* ID (UID - Chave Primária)
* ID do Instrutor (UID - Chave estrangeira)
* Modelo (String)
* Ano (String)
* Câmbio (Enum: "Automático", "Manual")
* Placa (String)
* CRLV (Imagem)
* Fotos do veículo (Imagem)
* Categoria_Veiculo (Enum: "A", "B")

**Agendamento_Aula**
* ID_Aula (UID - Chave Primária)
* ID_Aluno (UID - Chave Estrangeira)
* ID_Instrutor (UID - Chave Estrangeira)
* Data_Aula (Date)
* Hora_Inicio (Time)
* Hora_Fim (Time)
* Status (Enum: "Pendente_Confirmacao", "Confirmada", "Em_Andamento", "Concluida", "Cancelada", "Cancelada_No_Show")
* Endereco_Referencia_Busca (String)
* Latitude_Referencia (Float)
* Longitude_Referncia (Float)
* Valor_Total (Float)
* Valor_Hora_Acordado (Float)
* Cancelada_Por (Enum: "Aluno", "Instrutor", "Sistema")
* Motivo_Cancelamento (String)
* Disclaimer_Pagamento_Aceito (Boolean)
* PIN_Seguranca (String, 4 dígitos gerados aleatoriamente pelo sistema)

**Avaliação**
* ID_Avaliacao (UID - Chave Primária)
* ID_Aula (UID - Chave Estrangeira, Única)
* ID_Aluno (UID - Chave Estrangeira)
* ID_Instrutor (UID - Chave Estrangeira)
* Nota (Integer: 1 a 5)
* Comentario (Text)
* Data_Avaliacao (Timestamp)

**Mensagem_Chat**
* ID_Mensagem (UID - Chave Primária)
* ID_Aula (UID - Chave Estrangeira, vincula a mensagem a um agendamento específico)
* ID_Remetente (UID - Pode ser o ID do Aluno ou do Instrutor)
* Conteudo_Mensagem (Text)
* Lida (Boolean)
* Data_Hora_Envio (Timestamp)