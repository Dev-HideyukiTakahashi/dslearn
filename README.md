## dslearn
---

Bootcamp dev superior - Domínio e ORM, autorizações

---

* Competências
   * Domínio e ORM
   * Implementação de um modelo de domínio complexo (projeto DSLearn)
   * Instanciação (seed) de um modelo de domínio com SQL
* Autorizações
   * Autorização customizada em nível de serviço
   * Conteúdo customizado para o usuário logado
   * Refresh token
   * Pré-autorização de métodos

---
### Anotações sobre ORM 


##### Annotations

```
// Para facilitar string no banco de dados, sem essa anotação o enum salve em int na ordem inserida
@Enumerated(EnumType.STRING) 
private ResourceType type;
  
```

```
//Evita erros com atributos que manipulam data/hora no banco de dados
@Column(columnDefinition = "TIMESTAMP WITHOUT TIME ZONE")
private Instant moment;

```
---

##### Exemplo de @OneToMany

```
public class User 

@OneToMany(mappedBy = "user")   // apenas mapear com o nome do atributo na classe relacionada
private List<Notification> notifications = new ArrayList<>();
  
```

```
public class Notification

@ManyToOne
@JoinColumn(name = "user_id")  // criar a coluna para identificar qual id do usuario
private User user;
  
```

##### Exemplo de @ManyToMany

```
public class User 

// EAGER carrega todos os dados da associação, Lazy não carrega (default)
@ManyToMany(fetch = FetchType.EAGER) 
// Primeiro o nome da tabela, a coluna da qual pertence e a coluna da referência
@JoinTable(name = "tb_user_role", 
          joinColumns = @JoinColumn(name = "tb_user"),
          inverseJoinColumns = @JoinColumn(name = "tb_role")
)	
private Set<Role> roles = new HashSet<>();

```


##### Exemplo de @ManyToMany com Primary Key

```
// Class que vai implementar a PK

public class Enrollment implements Serializable {

	private static final long serialVersionUID = 1L;

	@EmbeddedId  // annotation para identificar implementação da classe EnrollmentPK como id
	private EnrollmentPK id;
  
  ....

```

```
// Class auxiliar PK

@Embeddable // annotation que define a classe como PK
public class EnrollmentPK implements Serializable {

	private static final long serialVersionUID = 1L;

	@ManyToOne      
	@JoinColumn(name = "user_id") // atributo que vai aparecer na tabela enrollment
	private User user;

	@ManyToOne
	@JoinColumn(name = "offer_id") // atributo que vai aparecer na tabela enrollment
	private Offer offer;
  
  ... constructors / gets, sets, hashcode e equals

```

```
// class que implementa @ManyToOne 
public class Deliver 

  // inclui as 2 colunas de referência
	@JoinColumns({@JoinColumn(name = "user_id"),
				       @JoinColumn(name = "offer_id")}
  )
	private Enrollment enrollment;
```

```
// class que implementa @ManyToMany
public class Lesson

	@ManyToMany
	@JoinTable(name = "tb_lessons_done", 
             joinColumns = @JoinColumn(name = "lesson_id"),
					   inverseJoinColumns = {
							@JoinColumn(name="user_id"),
							@JoinColumn(name="offer_id")
					   }
	)
	private Set<Enrollment> enrollmentsDone = new HashSet<>();
```
