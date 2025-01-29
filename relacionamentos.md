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
