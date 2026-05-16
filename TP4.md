# TP4 - REST : Gestion du Personnel

**Objectif :** Créer une API REST de gestion d'employés (CRUD) avec Maven, Spring Boot et IntelliJ IDEA, en comprenant l'architecture en couches et la communication client–serveur via JSON.

---

## I. Les endpoints exposés

| Méthode HTTP | URL | Action |
|---|---|---|
| `GET` | `/api/employees` | Récupérer tous les employés |
| `GET` | `/api/employees/{id}` | Récupérer un employé par son ID |
| `POST` | `/api/employees` | Créer un nouvel employé (corps JSON) |
| `PUT` | `/api/employees/{id}` | Mettre à jour tous les champs d'un employé |
| `PATCH` | `/api/employees/{id}` | Mettre à jour un seul champ d'un employé |
| `DELETE` | `/api/employees/{id}` | Supprimer un employé |

---

## II. Architecture en couches

L'application suit une architecture à 4 couches qui se chaînent de haut en bas :

```
Client (Navigateur / Postman / PowerShell)
        │  Requêtes HTTP (GET, POST, PUT, DELETE)
        ▼
  ┌─────────────┐
  │ Controller  │  ← Reçoit les requêtes HTTP et retourne les réponses JSON
  └──────┬──────┘
         │
  ┌──────▼──────┐
  │   Service   │  ← Contient la logique métier (règles de gestion)
  └──────┬──────┘
         │
  ┌──────▼──────┐
  │ Repository  │  ← Accès à la base de données (CRUD automatique via JPA)
  └──────┬──────┘
         │
  ┌──────▼──────┐
  │  Database   │  ← Base H2 en mémoire (pour les tests)
  └─────────────┘
```

> 💡 **Analogie :** Le Controller est comme un réceptionniste, le Service est le chef de projet, le Repository est l'archiviste, et la Database est la salle des archives.
> <br>**Repository:** hia linterface li te5dem les requêtes SQL 3al base de données, 3andha des méthodes (requêtes) prédéfinies (findAll, save, delete...) w te9bel zada requetes personnalisées (@Query ('requete...'))
> <br>**Service** yesta3melha, ya3ni fi west code el service, tdeclari variable de type elrepository hadhika
---

## III. Structure du projet

```
personnel/
└── src/
    └── main/
        ├── java/gl2.example.personnel/
        │   ├── controller/
        │   │   └── EmployeeController.java   ← API REST
        │   ├── model/
        │   │   └── Employee.java             ← Entité (table BDD)
        │   ├── repository/
        │   │   └── EmployeeRepository.java   ← Accès données
        │   ├── service/
        │   │   └── EmployeeService.java      ← Logique métier
        │   └── PersonnelApplication.java     ← Point d'entrée
        └── resources/
            └── application.properties        ← Configuration
```

---

## IV. Implémentation des fichiers clés

### 1. `Employee.java` — Le modèle (entité)

C'est la représentation d'un employé en base de données. Les annotations JPA font le lien avec la table SQL automatiquement.

```java
package gl2.example.personnel.model;

import jakarta.persistence.*;

@Entity                          // Dit à JPA que cette classe = une table en BDD
public class Employee {

    @Id                          // Champ = clé primaire
    @GeneratedValue(strategy = GenerationType.IDENTITY)  // ID auto-incrémenté
    private Long id;

    private String name;
    private String position;
    private double salary;

    // Getters et Setters
    public Long getId() { return id; }
    public String getName() { return name; }
    public void setName(String name) { this.name = name; }
    public String getPosition() { return position; }
    public void setPosition(String position) { this.position = position; }
    public double getSalary() { return salary; }
    public void setSalary(double salary) { this.salary = salary; }
}
```

### 2. `EmployeeRepository.java` — L'accès aux données

En étendant `JpaRepository`, toutes les opérations CRUD (save, findById, findAll, delete…) sont **générées automatiquement**, sans écrire de SQL.

```java
package gl2.example.personnel.repository;

import gl2.example.personnel.model.Employee;
import org.springframework.data.jpa.repository.JpaRepository;

// JpaRepository<Employee, Long> : entité = Employee, type de l'ID = Long
public interface EmployeeRepository extends JpaRepository<Employee, Long> {
    // Rien à écrire : findAll(), findById(), save(), deleteById() sont déjà disponibles !
}
```

### 3. `EmployeeService.java` — La logique métier

C'est ici que la logique de l'application est codée. Le service utilise le Repository pour accéder aux données.

```java
package gl2.example.personnel.service;

import gl2.example.personnel.model.Employee;
import gl2.example.personnel.repository.EmployeeRepository;
import org.springframework.stereotype.Service;
import java.util.List;
import java.util.Optional;

@Service   // Indique à Spring que c'est un composant de logique métier
public class EmployeeService {

    private final EmployeeRepository repository;

    // Spring injecte automatiquement le repository (injection de dépendance)
    public EmployeeService(EmployeeRepository repository) {
        this.repository = repository;
    }

    public List<Employee> getAllEmployees() {
        return repository.findAll();
    }

    public Optional<Employee> getEmployeeById(Long id) {
        return repository.findById(id);
    }

    public Employee createEmployee(Employee employee) {
        return repository.save(employee);
    }

    public void deleteEmployee(Long id) {
        repository.deleteById(id);
    }
}
```

### 4. `EmployeeController.java` — L'API REST

Le contrôleur expose les endpoints HTTP. Il reçoit les requêtes et délègue le travail au Service.

```java
package gl2.example.personnel.controller;

import gl2.example.personnel.model.Employee;
import gl2.example.personnel.service.EmployeeService;
import org.springframework.web.bind.annotation.*;
import java.util.List;

@RestController
@RequestMapping("/api/employees")   // Préfixe commun à tous les endpoints
public class EmployeeController {

    private final EmployeeService service;

    public EmployeeController(EmployeeService service) {
        this.service = service;
    }

    // GET /api/employees → liste tous les employés
    @GetMapping
    public List<Employee> getAll() {
        return service.getAllEmployees();
    }

    // GET /api/employees/1 → retourne l'employé avec id=1
    @GetMapping("/{id}")
    public Employee getById(@PathVariable Long id) {
        return service.getEmployeeById(id).orElseThrow();
    }

    // POST /api/employees → crée un employé à partir du corps JSON
    @PostMapping
    public Employee create(@RequestBody Employee employee) {
        return service.createEmployee(employee);
    }

    // DELETE /api/employees/1 → supprime l'employé avec id=1
    @DeleteMapping("/{id}")
    public void delete(@PathVariable Long id) {
        service.deleteEmployee(id);
    }
}
```

### 5. `application.properties` — Configuration de la base H2

```properties
spring.application.name=personnel

# Activer la console web H2 (accessible sur /h2-console)
spring.h2.console.enabled=true

# Base de données en mémoire
spring.datasource.url=jdbc:h2:mem:testdb
spring.datasource.driverClassName=org.h2.Driver
spring.datasource.username=sa
spring.datasource.password=password

spring.jpa.database-platform=org.hibernate.dialect.H2Dialect
spring.jpa.hibernate.ddl-auto=update
```

> 💡 **C'est quoi H2 ?** H2 est une base de données qui vit entièrement en mémoire RAM pendant l'exécution de l'application. Elle est idéale pour les tests car elle ne nécessite aucune installation. **Inconvénient :** toutes les données sont perdues à l'arrêt de l'application.

---

## V. Lancer et tester l'API

### Lancer l'application

Dans IntelliJ, faire un clic droit sur `PersonnelApplication.java` → **Run**, ou via Maven :

```bash
mvn spring-boot:run
```

L'API est disponible sur : `http://localhost:8080/api/employees`

---

### Tester avec le navigateur (GET uniquement)

Le navigateur ne supporte que les requêtes GET :

```
http://localhost:8080/api/employees       → liste tous les employés
http://localhost:8080/api/employees/1     → détails de l'employé n°1
```

---

### Tester avec Postman (toutes les méthodes)

Postman permet d'envoyer toutes les méthodes HTTP (GET, POST, PUT, PATCH, DELETE).

**GET — Liste tous les employés**
```
Méthode : GET
URL     : http://localhost:8080/api/employees
```

**GET — Un employé par ID**
```
Méthode : GET
URL     : http://localhost:8080/api/employees/1
```

**POST — Créer un employé**
```
Méthode : POST
URL     : http://localhost:8080/api/employees
Body (raw / JSON) :
{
  "name": "Hedi",
  "position": "Professeur",
  "salary": 4500
}
```

**PUT — Mettre à jour tous les champs**
```
Méthode : PUT
URL     : http://localhost:8080/api/employees/1
Body (raw / JSON) :
{
  "name": "Salma",
  "position": "Directrice",
  "salary": 5000
}
```

**PATCH — Mettre à jour un seul champ**
```
Méthode : PATCH
URL     : http://localhost:8080/api/employees/1
Body (raw / JSON) :
{
  "salary": 5500
}
```

**DELETE — Supprimer un employé**
```
Méthode : DELETE
URL     : http://localhost:8080/api/employees/1
```

---

### Tester avec PowerShell (Windows)

```powershell
# GET — Récupérer tous les employés
Invoke-WebRequest -Uri "http://localhost:8080/api/employees" -Method GET

# POST — Créer un employé
Invoke-WebRequest -Uri "http://localhost:8080/api/employees" `
  -Method POST `
  -Headers @{ "Content-Type" = "application/json" } `
  -Body '{"name": "John Doe", "position": "Developer", "salary": 50000}'

# PUT — Mettre à jour l'employé n°1
Invoke-WebRequest -Uri "http://localhost:8080/api/employees/1" `
  -Method PUT `
  -Headers @{ "Content-Type" = "application/json" } `
  -Body '{"name": "Jane Doe", "position": "Senior Developer", "salary": 60000}'

# DELETE — Supprimer l'employé n°1
Invoke-WebRequest -Uri "http://localhost:8080/api/employees/1" -Method DELETE

# Afficher tous les détails d'une réponse
$response = Invoke-WebRequest -Uri "http://localhost:8080/api/employees" -Method GET
$response | Format-List *
```

---

## VI. Récapitulatif des annotations Spring utilisées

| Annotation | Rôle |
|---|---|
| `@SpringBootApplication` | Point d'entrée, active la config automatique |
| `@Entity` | La classe est une table en base de données |
| `@Id` | Le champ est la clé primaire |
| `@GeneratedValue` | L'ID est généré automatiquement |
| `@Service` | Composant de logique métier |
| `@RestController` | Contrôleur qui retourne du JSON directement |
| `@RequestMapping` | Préfixe d'URL commun pour tous les endpoints |
| `@GetMapping` | Répond aux requêtes HTTP GET |
| `@PostMapping` | Répond aux requêtes HTTP POST |
| `@PutMapping` | Répond aux requêtes HTTP PUT |
| `@DeleteMapping` | Répond aux requêtes HTTP DELETE |
| `@PathVariable` | Récupère `{id}` depuis l'URL |
| `@RequestBody` | Désérialise le corps JSON en objet Java |
