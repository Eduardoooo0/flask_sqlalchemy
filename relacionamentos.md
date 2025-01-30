# Relacionamentos
## **Relacionamento 1:N**
```py
# Função usada no relacionamento 1:N e N:N
# Serve para vincular dois modelos
from sqlalchemy.orm import relationship
```
- **Classe User:**
```py
class User(Base):
    __tablename__ = 'users'
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(unique=True)
    recipes = relationship(backref='user') # Pega as receitas de um user.
```
- **Classe Recipes:**
```py
class Recipe(Base):
    __tablename__ = 'recipes'
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str] = mapped_column(String(100))
    user_id: Mapped[int] = mapped_column(ForeignKey("users.id")) # Define a chave estrangeira (Lembrar de importar a Classe ForeignKey) 
```
- **A função relationship define a classe do relacionamento com
usuário.**
- **o atributo ```backref``` indica que a receita tem uma referência para
o usuário que está vinculado a ela.**

- **Uso do backref:**
```py
user = session.query(User).first() # Pega todos os dados do primeiro user da tabela User
print(user.recipes) # Exibe as Receitas do usuário
recipe = session.query(Recipe).first() # Pega todos os dados da primeira receita da tabela Recipes
print(recipe.user) # Exibe o usuário vinculado a receita
```

## **Relacionamento N:N**
- **Neste tipo de relacionamento é necessário uma classe intermediária para fazer o relacionamento entre duas tabelas**

- **Classe Intermediária:**
```py
# Importa tudo para criar uma nova tabela
from sqlalchemy import Table, ForeignKey, Column, String

# Criação da nova tabela
student_course_table = Table(
"student_course",
Base.metadata,
# Chave estrangeira do estudante
Column("student_id", ForeignKey("students.id"), primary_key=True),
# Chave estrangeira do curso
Column("course_id", ForeignKey("courses.id"), primary_key=True),
)
```
- **Classe Estudantes:**
```py
# Tabela para estudantes
class Student(Base):
    __tablename__ = "students"
    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
    name: Mapped[str] = mapped_column(String, nullable=False)
    age: Mapped[int] = mapped_column(nullable=False)
    # Relacionamento com cursos por meio da tabela intermediária
    courses: Mapped[list["Course"]] = relationship(
    secondary=student_course_table, back_populates="students"
    )
```
- **Classe Cursos:**
```py
class Course(Base):
    __tablename__ = "courses"
    id: Mapped[int] = mapped_column(primary_key=True, autoincrement=True)
    name: Mapped[str] = mapped_column(String, nullable=False)
    # Relacionamento com estudantes por meio da tabela intermediária
    students: Mapped[list["Student"]] = relationship(
    secondary=student_course_table, back_populates="courses"
    )
```

- **Uso do back_populates:**
```py
# o back_populates faz com que seja possível usar o atributo students para ver os alunos de certo curso
courses: Mapped[list["Course"]] = relationship(secondary=student_course_table, back_populates="students"
print(Course.students) # Retorna os estudantes dos cursos

# Do mesmo jeito funciona com estudantes

# o back_populates faz com que seja possível usar o atributo courses para ver os cursos de certo aluno
students: Mapped[list["Student"]] = relationship(secondary=student_course_table, back_populates="courses")
print(Student.courses) # Retorna os cursos dos estudantes
)
```
## **Autorelacionamento**
- **Classe User:**
```py 
class User(Base):
    __tablename__ = 'users'
    id:Mapped[int] = mapped_column(primary_key=True)
    nome:Mapped[str]
    gerente_id:Mapped[int] = mapped_column(ForeignKey('users.id'), nullable=True) # Chave estrangeira
    gerenciados:Mapped[List['User']] = relationship('User', back_populates='gerente') # Define um relacionamento com gerente
    gerente = relationship('User', back_populates='gerenciados', remote_side=[id]) # Define um relacionamento com os gerenciados
```
- **Adicionando usuários:**
```py
user1 = User(nome='Eduardo') # Define um user gerente pois não tem chave estrangeira
session.add(user1)
user2 = User(nome='Lucas', gerente_id=1) # Define um user gerenciado pelo user com id = 1
user3 = User(nome='Dudu', gerente_id=1)
session.add_all([user2, user3])
session.commit()
print(user1.gerente)
#None, não possui gerente
print(user1.gerenciados)
# lista com os users gerenciados
```
