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
**在Instructor類別中反映射Course類別中的instructor屬性所對應的instructor_id以找到課程項目**
```
@OneToMany(mappedBy="instructor",
				cascade= {CascadeType.PERSIST, CascadeType.MERGE,
							CascadeType.DETACH, CascadeType.REFRESH})
	private List<Course> courses;
```
**由於雙向關係，透過此便捷方法加入新課程**
```
// add convenience methods for bi-directional relationship
	public void add(Course tempCourse)
	{
		if (courses == null) courses = new ArrayList<>();
		
		courses.add(tempCourse);
		tempCourse.setInstructor(this);
	}
```
**以下例子為示範創建新課程並加入到Instructor講師數據中**
```
public class CreateCoursesDemo {

	public static void main(String[] args) {
		
		// create session factory
		SessionFactory factory = new Configuration()
				.configure("hibernate.cfg.xml")
				.addAnnotatedClass(Instructor.class)
				.addAnnotatedClass(InstructorDetail.class)
				.addAnnotatedClass(Course.class)
				.buildSessionFactory();
		
		// create session
		Session session = factory.getCurrentSession();
		
		try {  											
            // start transaction
            session.beginTransaction();
            
            // get the instructor from db
            int theId = 1;
            Instructor tempInstructor = session.get(Instructor.class, theId);
            
            // create some courses
            Course tempCourse1 = new Course("Air Guitar - The Ultimate Guide");
            Course tempCourse2 = new Course("The Pinball Masterclass");
            
            // add courses to instructor
            tempInstructor.add(tempCourse1);
            tempInstructor.add(tempCourse2);
            
            // save the courses 
            session.save(tempCourse1);
            session.save(tempCourse2);

            // commit transaction
            session.getTransaction().commit();
 
            System.out.println("Done!");
            
        } catch (Exception exc) {
            exc.printStackTrace();
        } finally {
        	
        	// add clean up code
        	session.close();
        	
            factory.close();
        }
	}
}
```
**以下示例為示範刪除課程，由於級聯關係不包含刪除動作，因此當刪除課程時並不會一同刪除講師資訊**
```
public static void main(String[] args) {
		
		// create session factory
		SessionFactory factory = new Configuration()
				.configure("hibernate.cfg.xml")
				.addAnnotatedClass(Instructor.class)
				.addAnnotatedClass(InstructorDetail.class)
				.addAnnotatedClass(Course.class)
				.buildSessionFactory();
		
		// create session
		Session session = factory.getCurrentSession();
		
		try {  											
            // start transaction
            session.beginTransaction();
            
            // get a course
            int theId = 12;
            Course tempCourse = session.get(Course.class, theId);
            
            // delete course
            System.out.println("Deleting course: " + tempCourse);
            session.delete(tempCourse);

            // commit transaction
            session.getTransaction().commit();
 
            System.out.println("Done!");
            
        } catch (Exception exc) {
            exc.printStackTrace();
        } finally {
        	
        	// add clean up code
        	session.close();
        	
            factory.close();
        }
	}
}
```
