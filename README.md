
# CallenMine
Essa API tem objetivo de ser um serviço de agendamento que possa ser consumido por diversas aplicações.

## Modelo de Dados
![image](https://user-images.githubusercontent.com/1299063/187514451-ab771e32-aa94-4cbf-85d5-3313da6fa5ea.png)

## Endpoints esperados
- [ ] **EP.1** `GET` /customer/{ document }
- [ ] **EP.2** `GET` /scheduling
- [ ] **EP.3** `GET` /scheduling/{ scheduling_id }
- [ ] **EP.4** `POST` /expert/scheduling/
- [ ] **EP.5** `GET` /expert/scheduling/{ expert_id }
- [ ] **EP.6** `GET` /expert/occupied/{ expert_id }
- [ ] **EP.7** `GET` /expert/free?date={dd-MM-YYYY}
- [ ] **EP.8** `POST` /blocking/scheduling
- [ ] **EP.9** `GET` /blocking/scheduling

## Contratos

##### EP.1 - Buscar cliente `GET /customer/{ document }`.
Busca no banco por um cliente passando como parametro o documento do cliente.
- `Status 200` Quando encontra o cliente, retorna o objeto ==customer== com os dados do cliente.
- `Status 404` Quando não encontra o cliente retorna um status not found
- `Status 408` Quando o request não é do tipo `GET`

##### EP.2 - Pega todos os agendamentos `GET /scheduling`.
Retorna uma lista com todos os agendamentos contendo as datas, técnicos e cliente do agendamento.
- `Status 200` Retorna um array de agendamentos no formato de objeto de scheduling (modelo de dados Scheduling)

##### EP.3 - Pega todos os agendamentos `GET /scheduling/{ scheduling_id }`.
Retorna informações sobre um agendamento em específico.
- `Status 200` Quando encontra o agendamento, retorna o objeto no formato de ==scheduling == (do modelo de dados aqui) com os dados do agendamento.
- `Status 404` Quando não encontra o agendamento retorna um status not found
- `Status 408` Quando o request não é do tipo `GET`
- 
##### EP.4 - Cria um agendamento `POST /expert/scheduling/`.
Cria um agendamento recebendo como informações o json seguinte, sendo que a primeira data de agendamento não pode passar de 90 dias da data atual.
```json
{
	"customer": "00.000.000/0000-00",
	"note": "Lorem ipsum dolor sit amet, consectetur adipiscing elit.",
	"schedules": [
		{ "date" : "Aug 30 2022", "experts_id": [ 1, 2 ] }, // until 90d after today
		{ "date" : "Aug 31 2022", "experts_id": [ 2 ] },
		{ "date" : "Sep 1 2022", "experts_id": [ 1 ] },
	]
}
``` 
- `Status 200` Quando a inserção é feita retorna o id do agendamento feito.
- `Status 400` Quando alguma informação é ausente ou algo da errado
- `Status 408` Quando o request não é do tipo `POST`

##### EP.5 - Pega os agendamentos `GET /expert/scheduling/{ expert_id }`.
Retorna uma lista com todos os agendamentos de um técnico específico contendo as datas, técnicos (porque pode ter outros envolvidos) e cliente do agendamento.
- `Status 200` Retorna um array de agendamentos no formato de objeto de scheduling (modelo de dados Scheduling)

##### EP.6 - Pega uma lista de todas as datas indisponíveis para um técnico `GET /expert/occupied/{expert_id}`.
Retorna uma lista com todos as datas indisponíveis/ocupadas para agendamento da data atual até 90 dias a frente do técnico especificado.
- `Status 200` Retorna um array no formato seguinte
```json
[
	{ "date": "dd-MM-YYYY", "type": "scheduling", "message":"Nome do cliente" },
	{ "date": "dd-MM-YYYY", "type": "blocking", "message":"Nota do bloqueio" }
]
```

##### EP.7 - Pega uma lista de técnicos livres `GET /expert/free?date=dd-MM-YYYY`.
Retorna uma lista de técnicos livres na data especificada.
- `Status 200` Retorna um array de técnicos
- `Status 204` Quando não tem técnicos livres na data
- `Status 400` Quando a data é inválida
- `Status 408` Quando o request não é do tipo `GET`

##### EP.8 - Cria um bloqueio na agenda `POST /expert/scheduling/`.
Cria um bloqueio de agenda recebendo como informações o json seguinte:
```json
{
	"start": "Sep 7 2022",
	"end": "Sep 7 2022",
	"note": "Independece Day"
}
``` 
- `Status 200` Quando a inserção é feita retorna o id do bloqueio feito.
- `Status 400` Quando alguma informação é ausente ou algo da errado
- `Status 408` Quando o request não é do tipo `POST ou GET`

##### EP.9 - Pega todos os bloqueios na agenda `GET /expert/scheduling/`.
Retorna uma lista com todos os bloqueios.
- `Status 200` Retorna um array bloqueios


## Modelo de dados específicos

#### Scheduling
| Coluna   | Tipo      | Descrição                             |
|----------|-----------|---------------------------------------|
| id       | `INT`     | id único do agendamento.              |
| customer | `OBJ`     | Objeto do tipo Customer               |
| note     | `Text`    | Nota de descrição sobre o agendamento |
| dates    | `Dates[]` | Array de agendamentos                 |

#### Dates
| Coluna | Tipo        | Descrição                                |
|--------|-------------|------------------------------------------|
| id     | `INT`       | id único do agendamento.                 |
| date   | `Date`      | Data do agendamento                      |
| expert | `Expert []` | Array de técnicos pra data em específico |
