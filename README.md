# Conversor de Eventos do Google Calendar para Tarefas do Google Tasks
Este projeto é um script do Google Apps que converte automaticamente eventos de um calendário específico do Google Calendar em tarefas em uma lista específica do Google Tasks. Os eventos com títulos começando com "read " são identificados e transformados em tarefas. O script então exclui o evento original do calendário.

## Recursos Principais
* **Conversão Automática de Evento para Tarefa:** Converte eventos prefixados com "read " do calendário em tarefas do Google Tasks.
* **Exclusão de Eventos:** Exclui o evento do calendário após a criação da tarefa.
* **Tratamento de Erros:** Inclui blocos `try-catch` para lidar com possíveis erros durante a criação de tarefas e exclusão de eventos. Mensagens de erro detalhadas são registradas.
* **Configuração:** Variáveis `taskListId` e `calendarId` facilmente configuráveis.
* **Rastreamento da Última Execução:** Mantém o controle da última execução bem-sucedida para evitar a criação duplicada de tarefas.

## Tecnologias Utilizadas
* Google Apps Script
* API do Google Calendar
* API do Google Tasks

## Pré-requisitos
* Uma conta do Google.
* Acesso ao Google Calendar e ao Google Tasks.

## Instalação
1. **Acesse o Google Apps Script:** Abra seu Google Drive e crie um novo projeto do Google Apps Script.
2. **Copie o Código:** Copie o conteúdo do arquivo `eventoparatarefa-googleappsscript.json` (fornecido no repositório) e cole-o no editor de scripts.
3. **Configure o Script:** Localize as seguintes variáveis no início da função `replaceReadEventsWithTasks` e substitua os valores de espaço reservado pelos seus próprios:
   ```javascript
   var taskListId = 'YOUR_GOOGLE_TASKS_LIST_ID';  // Substitua pelo ID da sua lista de tarefas do Google Tasks
   var calendarId = 'YOUR_GOOGLE_CALENDAR_ID';    // Substitua pelo ID do seu Google Calendar
   ```
   Para encontrar o ID da sua Lista de Tarefas do Google e o ID do Google Calendar, use as funções auxiliares `listTaskLists()` e `listCalendars()` dentro do script. Execute essas funções individualmente para ver uma lista dos seus calendários e listas de tarefas com seus respectivos IDs no log de execução.

4. **Salve o Projeto:** Salve o projeto com um nome descritivo (por exemplo, "CalendarToTasks").

## Uso
O script é executado automaticamente com base em um gatilho controlado por tempo (configurado abaixo). No entanto, você pode executá-lo manualmente a qualquer momento clicando no botão "Executar" no editor de scripts e selecionando a função `replaceReadEventsWithTasks`. Você precisará autorizar o script a acessar seu Google Calendar e Google Tasks.

### Configuração de Gatilho Controlado por Tempo:
1. Vá para "Editar" -> "Gatilhos do projeto atual".
2. Clique em "+ Adicionar Gatilho".
3. Configure o seguinte:
    * Função para executar: `replaceReadEventsWithTasks`
    * Eventos: Controlado por tempo
    * Tipo: No menu suspenso, selecione "Ao abrir" ou "Baseado em tempo". Escolha sua frequência preferida (por exemplo, a cada hora, todos os dias).
    * Salve.
Isso executará automaticamente o script no intervalo especificado. O script processará apenas eventos adicionados ou modificados desde a última execução.

## Configuração
O script é configurado principalmente através das variáveis `taskListId` e `calendarId`. Essas devem ser atualizadas com o ID real da sua lista de tarefas do Google e o ID do Google Calendar, respectivamente.

## Estrutura do Projeto
O projeto consiste em um único arquivo do Google Apps Script: `eventoparatarefa-googleappsscript.json`, contendo o código para a conversão de eventos em tarefas.

## Licença
Este projeto está licenciado sob a Licença MIT - consulte o arquivo `LICENSE` para obter detalhes.

## Tratamento de Erros e Solução de Problemas
* **"Calendário não encontrado!"** Este erro ocorre se o `calendarId` estiver incorreto. Verifique novamente o ID obtido usando a função `listCalendars()`.
* **"Erro ao criar tarefa: ..."** Isso pode ser devido a vários motivos, incluindo problemas de rede, `taskListId` incorreto ou permissões insuficientes. Examine a mensagem de erro detalhada registrada pelo script para obter orientações mais específicas.
* **"Erro ao excluir evento: ..."** Semelhante ao erro de criação de tarefa, isso pode ser causado por problemas de rede, permissões ou outras circunstâncias imprevistas. Consulte a mensagem de erro detalhada registrada pelo script.