# TP3 - REST Calculator

**Objectif :** Créer une calculatrice simple exposée sous forme d'API REST en utilisant IntelliJ IDEA, Maven et Spring Boot.

L'application expose des endpoints pour les différentes opérations de la calculatrice :

| Opération | URL |
|---|---|
| Addition | `http://localhost:8080/add?a=5&b=3` |
| Soustraction | `http://localhost:8080/subtract?a=5&b=3` |
| Multiplication | `http://localhost:8080/multiply?a=5&b=3` |
| Division | `http://localhost:8080/divide?a=6&b=3` |

Chaque URL prend deux paramètres `a` et `b` pour effectuer l'opération correspondante.

---

## 2. Configurer les fichiers du projet

### 2.1 Fichier `pom.xml`

Vérifier que le fichier `pom.xml` contient les dépendances nécessaires pour Spring Web :

```xml
<dependencies>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-web</artifactId>
    </dependency>
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-test</artifactId>
        <scope>test</scope>
    </dependency>
</dependencies>

<build>
    <plugins>
        <plugin>
            <groupId>org.springframework.boot</groupId>
            <artifactId>spring-boot-maven-plugin</artifactId>
        </plugin>
    </plugins>
</build>
```

### 2.2 Classe principale `CalculatorApplication.java`

La classe principale démarre l'application Spring Boot :

```java
// Déclaration du package de l'application
package gl2.example.calculator;

// Importation des classes nécessaires de Spring Boot
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

// @SpringBootApplication active plusieurs fonctionnalités de Spring Boot :
// la configuration automatique (AutoConfiguration)
// et la détection des composants (ComponentScan).
@SpringBootApplication
public class CalculatorApplication {

    // Méthode principale (point d'entrée de l'application)
    public static void main(String[] args) {
        // Lancement de l'application Spring Boot
        SpringApplication.run(CalculatorApplication.class, args);
    }
}
```

---

## 3. Créer la classe contrôleur `CalculatorController.java`

### 3.1 C'est quoi un contrôleur REST (`@RestController`) ?

Un contrôleur Spring REST (`@RestController`) est une classe qui expose des services web RESTful. Il permet de gérer les requêtes HTTP et de retourner des données (JSON, XML ou texte brut) à un client.

### 3.2 Différence entre `@Controller` et `@RestController`

| Annotation | Utilisation principale | Retour |
|---|---|---|
| `@Controller` | Gère les requêtes et retourne une vue HTML | Une page HTML rendue par un moteur de templates (Thymeleaf, JSP…) |
| `@RestController` | Gère les requêtes et retourne des données (JSON, XML, texte brut…) | Une réponse envoyée directement au client, sans moteur de rendu |

### 3.3 Notre `CalculatorController.java`

Ajouter la classe `CalculatorController` dans le package `gl2.example.calculator` :

```java
package gl2.example.calculator;

import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.RequestParam;
import org.springframework.web.bind.annotation.RestController;

// Indique que cette classe est un contrôleur REST :
// ses méthodes retournent directement des données (texte, JSON…)
@RestController
public class CalculatorController {

    // Répond aux requêtes HTTP GET sur /add
    @GetMapping("/add")
    public double add(@RequestParam double a, @RequestParam double b) {
        return a + b;
    }

    // Répond aux requêtes HTTP GET sur /subtract
    @GetMapping("/subtract")
    public double subtract(@RequestParam double a, @RequestParam double b) {
        return a - b;
    }

    // Répond aux requêtes HTTP GET sur /multiply
    @GetMapping("/multiply")
    public double multiply(@RequestParam double a, @RequestParam double b) {
        return a * b;
    }

    // Répond aux requêtes HTTP GET sur /divide
    // Vérifie que le diviseur n'est pas zéro
    @GetMapping("/divide")
    public double divide(@RequestParam double a, @RequestParam double b) {
        if (b == 0) {
            throw new IllegalArgumentException("Cannot divide by zero.");
        }
        return a / b;
    }
}
```

### 3.4 Explication des annotations utilisées

Une **annotation** en Java est une métadonnée qui ajoute des informations au code sans modifier son comportement directement. Elle fournit des instructions au compilateur, aux frameworks (Spring, Hibernate…) et aux outils de développement.

| Annotation | Description |
|---|---|
| `@RestController` | Indique que la classe est un contrôleur REST qui retourne des données directement |
| `@GetMapping("/add")` | La méthode répond aux requêtes HTTP GET sur l'URL `/add` |
| `@RequestParam` | Récupère un paramètre passé dans l'URL (`?a=...&b=...`) |

Les annotations se divisent en quatre catégories :

1. **Documentation** — génèrent la Javadoc : `@author`, `@version`, `@param`, `@return`
2. **Compilation** — influencent le compilateur : `@Override`, `@Deprecated`, `@SuppressWarnings`
3. **Exécution** — utilisées par les frameworks : `@RestController`, `@Service`, `@Autowired`, `@Entity`
4. **Test (JUnit)** — `@Test`, `@BeforeEach`, `@AfterEach`

---
> **Melle55r**, efhem lcontroller hakka: Houa lli bch y9arrar chnw ysir 7asb chnw yjih fel URL: ken fel url maktouba "add" (hadhia hia @GetMapping('/add')), rahou bch y5addam lmethode mta3 add, ken fl url multiply rahou bch y5addem multiply...
---
## 4. Démarrer l'application

Deux options pour lancer l'application :

**Option A — Via IntelliJ IDEA :**
Faire un clic droit sur `CalculatorApplication.java` → `Run 'CalculatorApplication'`

**Option B — Via la commande Maven :**
```bash
mvn spring-boot:run
```

---

## 5. Vérifier que l'application est en cours d'exécution

Une fois démarrée, le message suivant apparaît dans la console :

```
Tomcat started on port(s): 8080 (http)
```

---

## 6. Tester les endpoints REST

Ouvrir un navigateur web et saisir les URLs suivantes :

| Opération | URL de test | Résultat attendu |
|---|---|---|
| Addition | `http://localhost:8080/add?a=10&b=5` | `15.0` |
| Soustraction | `http://localhost:8080/subtract?a=5&b=3` | `2.0` |
| Multiplication | `http://localhost:8080/multiply?a=5&b=3` | `15.0` |
| Division | `http://localhost:8080/divide?a=6&b=3` | `2.0` |

**Décryptage d'une URL de test :**

```
http://localhost:8080/add?a=10&b=5
│         │      │    │   └──────── paramètres a=10 et b=5
│         │      │    └──────────── endpoint défini dans le contrôleur
│         │      └───────────────── port par défaut de Spring Boot
│         └──────────────────────── serveur local (votre machine)
└────────────────────────────────── protocole HTTP
```

Pour tester d'autres valeurs, il suffit de modifier `a` et `b` dans la barre d'adresse. Par exemple :

```
http://localhost:8080/add?a=20&b=8  →  28.0
```

---

## 7. Gérer les erreurs éventuelles

| Problème | Cause probable | Solution |
|---|---|---|
| "Page non trouvée" | L'application n'est pas démarrée | Relancer via IntelliJ ou `mvn spring-boot:run` |
| "Site inaccessible" | Le port 8080 est occupé | Vérifier qu'aucune autre application n'utilise le port 8080 |
| Erreur division | `b = 0` passé en paramètre | Une exception `IllegalArgumentException` est levée automatiquement |
