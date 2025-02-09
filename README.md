

function replaceReadEventsWithTasks() {
  // Substitua pelo ID da sua lista de tarefas no Google Tasks.
  // Para obter o ID, execute a função 'listTaskLists()' abaixo e copie o ID desejado.
  var taskListId = 'MDJGUlJoQ1h4OTNocWVpYw'; 

  // Substitua pelo ID do calendário que deseja monitorar.
  // Para obter o ID, execute a função 'listCalendars()' abaixo e copie o ID desejado.
  var calendarId = 'f64bdd376da85a5eb056d22f293d407626a3e8f8e59b49be0393da78960b38df@group.calendar.google.com'; 

  // Obtém as propriedades do script para armazenar o timestamp da última execução.
  var properties = PropertiesService.getScriptProperties();
  var lastRun = properties.getProperty('LAST_RUN_TIMESTAMP');
  var now = new Date();

  // Define o intervalo de datas para buscar eventos:
  // - Se houver um timestamp da última execução, use-o como data inicial.
  // - Caso contrário, considere o início do dia atual.
  var startDate = lastRun ? new Date(parseInt(lastRun)) : new Date().setHours(0, 0, 0, 0);
  var endDate = new Date();
  endDate.setDate(endDate.getDate() + 1); // Até o final do dia atual.
  endDate.setHours(23, 59, 59, 999);

  // Armazena o timestamp atual como a última execução.
  properties.setProperty('LAST_RUN_TIMESTAMP', now.getTime().toString());

  // Obtém o calendário pelo ID fornecido.
  var calendar = CalendarApp.getCalendarById(calendarId);
  if (!calendar) {
    Logger.log("Calendário não encontrado!");
    return;
  }

  // Busca todos os eventos no intervalo de datas definido.
  var events = calendar.getEvents(startDate, endDate);

  // Itera sobre os eventos encontrados.
  for (var i = 0; i < events.length; i++) {
    var event = events[i];
    var title = event.getTitle().trim(); // Remove espaços extras no título.
    var lastUpdated = event.getLastUpdated();

    // Verifica se o título do evento começa com "read" (case-insensitive).
    if (title.toLowerCase().startsWith('read ')) {
      var dueDate = event.getStartTime(); // Data de início do evento.

      // Cria uma nova tarefa no Google Tasks com base no evento.
      var task = {
        title: title,
        due: dueDate.toISOString().replace(/\.\d+Z$/, "Z") // Formata a data no padrão RFC 3339.
      };

      try {
        // Insere a tarefa na lista de tarefas especificada.
        Tasks.Tasks.insert(task, taskListId);
        Logger.log("Tarefa criada: " + title);
      } catch (e) {
        Logger.log("Erro ao criar tarefa: " + e.toString()); // Registra erros detalhados.
      }

      try {
        // Exclui o evento do calendário após criar a tarefa.
        event.deleteEvent();
        Logger.log("Evento excluído: " + title);
      } catch (e) {
        Logger.log("Erro ao excluir evento: " + e.toString());
      }
    }
  }
}

// Função auxiliar para listar todas as listas de tarefas disponíveis no Google Tasks.
// Execute esta função para encontrar o ID da lista de tarefas que deseja usar.
function listTaskLists() {
  var taskLists = Tasks.Tasklists.list().getItems();
  for (var i = 0; i < taskLists.length; i++) {
    Logger.log('Título da Lista: ' + taskLists[i].getTitle() + ' | ID da Lista: ' + taskLists[i].getId());
  }
}

// Função auxiliar para listar todos os calendários disponíveis no Google Calendar.
// Execute esta função para encontrar o ID do calendário que deseja monitorar.
function listCalendars() {
  var calendars = CalendarApp.getAllCalendars();
  for (var i = 0; i < calendars.length; i++) {
    Logger.log('Nome do Calendário: ' + calendars[i].getName() + ' | ID do Calendário: ' + calendars[i].getId());
  }
}
