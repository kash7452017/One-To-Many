## @OneToMany - Bi-Directional
>此示例為一個講師可以對應多個課程，同時也可以透過反應射找尋對應的講師，因此須注意的是在Course類別中使用的註釋為@ManyToOne，課程代表多方，對應的是單方的講師，在Course表中有一列資訊是代表所對應的講師ID值，以此資訊做為對應KEY來映射配置，因此在Course類別中使用@JoinColumn加入此列映射配置。
```
@Entity
@Table(name="course")
public class Course {
	
	// define our field
	
	// define constructors
	
	// define getter setters
	
	// define tostring
	
	// annotate fields
	
	@Id
	@GeneratedValue(strategy=GenerationType.IDENTITY)
	@Column(name="id")
	private int id;
	
	@Column(name="title")
	private String title;
	
	@ManyToOne(cascade= {CascadeType.PERSIST, CascadeType.MERGE, 
						CascadeType.DETACH, CascadeType.REFRESH})
	@JoinColumn(name="instructor_id")
	private Instructor instructor;
	
	public Course() {
		
	}

	public Course(String title) {
		this.title = title;
	}

	public int getId() {
		return id;
	}

	public void setId(int id) {
		this.id = id;
	}

	public String getTitle() {
		return title;
	}

	public void setTitle(String title) {
		this.title = title;
	}

	public Instructor getInstructor() {
		return instructor;
	}

	public void setInstructor(Instructor instructor) {
		this.instructor = instructor;
	}

	@Override
	public String toString() {
		return "Course [id=" + id + ", title=" + title + "]";
	}

}
```

