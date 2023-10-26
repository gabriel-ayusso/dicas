# Dicas do Google Apis

## Google Calendar

### Listar os calendários

Para listar os calendários, executar o método ```calendar/list```

https://developers.google.com/calendar/v3/reference/calendarList/list

### Agendar uma nova Reunião

Criar um novo evento, com link para o Google Meet 

https://developers.google.com/calendar/v3/reference/events/insert

```json
{
  "end": {
    "dateTime": "2020-12-10T14:30:00-03:00",
    "timeZone": "America/Sao_Paulo"
  },
  "start": {
    "dateTime": "2020-12-10T14:15:00-03:00",
    "timeZone": "America/Sao_Paulo"
  },
  "attendees": [
    {
      "email": "gabriel.ayusso@gmail.com"
    }
  ],
  "description": "Teste de meeting via API",
  "summary": "Reunião de teste",
  "conferenceData": {
    "createRequest": {
      "requestId": "5c952489-8a1c-401d-ac8c-a963e14d1467",
      "conferenceSolutionKey": {
        "type": "hangoutsMeet"
      }
    }
  }
}
```

