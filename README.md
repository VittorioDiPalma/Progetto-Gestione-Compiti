# Microservizio: Gestione Compiti

## Panoramica
Il microservizio **Gestione Compiti** è responsabile della creazione, assegnazione, aggiornamento e gestione dei compiti all'interno del sistema accademico. Le sue funzionalità principali includono:

- Creazione e assegnazione di compiti da parte dei docenti.
- Visualizzazione stato dei compiti assegnati.
- Visualizzazione dei compiti da parte degli studenti.
- Sottomissione dei compiti da parte degli studenti.
- Notifica agli altri microservizi (es. Valutazioni) tramite eventi RabbitMQ.

## Tech Stack
- **Framework:** Spring Boot  
- **Database:** PostgreSQL  
- **Message Broker:** RabbitMQ  
- **Documentazione API:** Swagger / OpenAPI 3.0

## Modello Dati

### DTO (Data Transfer Object)

#### `AssignmentDTO`
```java
public class AssignmentDTO {
    private Long id;
    private String title;
    private String description;
    private LocalDateTime dueDate;
    private Long courseId;
    private Long teacherId;
    private AssignmentStatus status; // Enum: CREATED, ASSIGNED, SUBMITTED, CLOSED
}
```

#### `AssignmentSubmissionDTO`
```java
public class AssignmentSubmissionDTO {
    private Long id;
    private Long assignmentId;
    private Long studentId;
    private LocalDateTime submissionDate;
    private String fileUrl;
    private SubmissionStatus status; // Enum: SUBMITTED, REVIEWED
}
```

### Entità JPA

#### `Assignment`
- `id`: Identificativo univoco del compito
- `title`: Titolo del compito
- `description`: Descrizione dettagliata
- `due_date`: Data di scadenza
- `course_id`: ID del corso associato
- `teacher_id`: ID del docente assegnante
- `status`: Stato del compito (CREATED, ASSIGNED, SUBMITTED, CLOSED)

#### `AssignmentSubmission`
- `id`: Identificativo univoco della sottomissione
- `assignment_id`: ID del compito associato
- `student_id`: ID dello studente
- `submission_date`: Data e ora della sottomissione
- `file_url`: URL del file sottomesso
- `status`: Stato della sottomissione (SUBMITTED, REVIEWED)

## API REST

### Endpoint per i Docenti
- `POST /api/v1/assignments`: Creazione di un nuovo compito
- `PUT /api/v1/assignments/{id}`: Aggiornamento di un compito esistente
- `GET /api/v1/assignments/course/{courseId}`: Elenco dei compiti per un corso specifico
- `GET /api/v1/assignments/{id}`: Dettagli di un compito specifico
- `DELETE /api/v1/assignments/{id}`: Eliminazione di un compito

### Endpoint per gli Studenti
- `GET /api/v1/assignments/available`: Elenco dei compiti disponibili
- `POST /api/v1/assignments/{id}/submit`: Sottomissione di un compito
- `GET /api/v1/assignments/submissions`: Elenco delle sottomissioni personali
- `GET /api/v1/assignments/submissions/{id}`: Dettagli di una sottomissione specifica

## Integrazione con Altri Microservizi

### Eventi Pubblicati su RabbitMQ
- `assignment.created`: Emesso quando un nuovo compito viene creato
- `assignment.submitted`: Emesso quando uno studente sottomette un compito
- `assignment.closed`: Emesso quando un compito viene chiuso

### Eventi Consumato da RabbitMQ
- `course.created`: Per sincronizzare i compiti con i nuovi corsi
- `user.registered`: Per associare gli studenti ai compiti
