# TP1- RMI : Hello

### Description TP:
* Une application RMI (Remote Method Invocation) avec Maven.
* Ce projet inclura un serveur RMI et un client RMI.
### Les fichier à créer:
1. Interface distante “RemoteInterface” : C'est l'interface que le client et le serveur
partagent. Elle définit les méthodes qui peuvent être appelées à distance.
2. Classe “Serveur” : Implémente l'interface distante et enregistre l'objet dans le
registre RMI pour que le client puisse le rechercher.
3. Calsse “Client” : Cherche l'objet distant dans le registre RMI et appelle ses
méthodes.
Étapes :
4. Créer un projet Maven : Ouvrir IntelliJ IDEA (IDE compatible Maven), puis créez un
nouveau projet Maven (nom du projet : tp1-rmi-HelloWorld).
5. Structure du projet :
```
tp1-rmi-HelloWorld/
├── pom.xml
└── src
└── main
├── java
│ ├── gl2
│ │ └── example
│ │ ├── Server.java
│ │ ├── Client.java
│ │ ├── RemoteInterface.java
└── resources
```
### Étape 1: Configuration pom.xml
IntelliJ IDEA prend en charge les fichiers pom.xml, qui sont utilisés pour gérer les
dépendances, les configurations de build, et autres aspects des projets Maven.
```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"

xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/maven-
v4_0_0.xsd">

<modelVersion>4.0.0</modelVersion>
<groupId>gl2.example</groupId>

<artifactId>rmi1</artifactId>
<version>1.0-SNAPSHOT</version>
<name>Archetype - rmi1</name>
<url>http://maven.apache.org</url>
<properties>
<maven.compiler.source>22</maven.compiler.source>
<maven.compiler.target>22</maven.compiler.target>
</properties>
<dependencies>
<!-- Aucune dépendance spécifique n'est nécessaire pour RMI -->
</dependencies>
</project>
```
### Étape 2: Interface distante RemoteInterface.java
```java
package gl2.example;
import java.rmi.Remote;
import java.rmi.RemoteException;
public interface RemoteInterface extends Remote {
  String sayHello(String msg) throws RemoteException;
}
```

### Étape 3: Implémentation du serveur Server.java
```java
import java.rmi.registry.Registry;
import java.rmi.server.UnicastRemoteObject;
import java.rmi.RemoteException;
import java.rmi.Naming;
public class Server extends UnicastRemoteObject implements RemoteInterface {
  //
  // // constructeur obligatoire
  protected Server() throws RemoteException {
    super();
  }
  // implémentation de la méthode distante
  @Override
  public String sayHello(String msg) throws RemoteException {
    System.out.println("Hello: " + msg.toUpperCase());
    return "Message Bien Reçu !";
}
public static void main(String[] args) {
  try {
    // créer le registry
    Registry registry = LocateRegistry.createRegistry(1099);
    // créer instance du serveur
    Server server = new Server();
    // enregistrer dans le registry
    registry.rebind("HelloService", server);
    // Lie l'objet à un nom dans le registre RMI (si vous avez utilisé start rmiregistry dans le terminal)
    //Naming.rebind("//localhost/HelloService", server);
    System.out.println("Server ready -->");
  }
  catch (Exception e) {
    System.out.println("Server failed <-- " + e);
    e.printStackTrace();
  }
  }
}
```
### Étape 4: Implémentation du client Client.java
```java
package gl2.example;
import java.util.Scanner;
import java.rmi.registry.LocateRegistry;
import java.rmi.registry.Registry;
public class Client {
public static void main(String[] args) {
  try {
    // Rechercher l'objet distant par son nom dans le registre RMI
    Registry registry = LocateRegistry.getRegistry("localhost", 1099);
    // Récupérer une instance locale de l’objet distant
    RemoteInterface stub = (RemoteInterface) registry.lookup("HelloService");
    // Appeler une méthode distante
    Scanner sc = new Scanner(System.in);
    System.out.println("Saisissez un texte: ");
    String str = sc.nextLine(); // Lit une ligne entière
    String response = stub.sayHello(str);
    System.out.println("Server Response: " + response);
  }
  catch (Exception e) {
    System.out.println("Client exception: " + e);
    e.printStackTrace();
  }
  }
}
```
### Étape 5: Compilation et exécution
1. Compiler le projet : Utilisez Maven pour compiler votre projet via la commande
```powershell
mvn clean install.
```
2. Lancer le serveur RMI : Démarrez le registre RMI (rmiregistry) en ligne de commande à partir du
répertoire target/classes :
```powershell
rmiregistry
```
--> Vous pouvez ne pas passer par cette étape si vous avez utilisé :
```java
registry.createRegistry() ;
```
Ensuite, lancez le serveur en exécutant la classe Server :
```powershell
java gl2.example.Server
```
3. Lancer le client RMI : Ouvrez une nouvelle fenêtre de terminal et exécutez le client :
```powershell
java gl2.example.Client
```

---

Melle55r, tabda bel **Interface** t7ott feha les méthodes, tkoun textendi men *Remote*. Hadhika *l'interface distante*.
Mn ba3d, fel **Serveur**, ykoun yimplmenti linterface hadhika w extendi men *UnicastRemoteObject*, 
  1. tdéfini fih les méthodes mta3 l'interface
  2. tasna3 registre RMI
  3. tasna3 instance mta3 Serveur
  4. tsajjel serveur hadhaka fl regsitre<br>

Mn ba3d, fel **Client**,
  1. ylawwj 3al registre hadhaka bel host wl port lli sajjalt fehom 
  2. ya5ou instance locale mel objet distant (lli 3andou lméthode distante haki "sayHello")
  3. yappelli les méthodes lli y7eb 3lehom
