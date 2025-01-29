# Configuração do flask sqlalchemy
## **1. Instalação:**
```bash
pip install flask
pip install flask-sqlalchemy
```
## **2. Configuração:**
No arquivo app.py, você pode adicionar o seguinte código:
```py
from flask import Flask
from flask_sqlalchemy import SQLAlchemy
from sqlalchemy.orm import DeclarativeBase, Mapped, mapped_column


app = Flask(__name__)

# Declara a classe Base
class Base(DeclarativeBase):
    pass

# Configuração do banco de dados
app.config['SQLALCHEMY_DATABASE_URI'] = 'sqlite:///banco_de_dados.db'  # Usando SQLite

# Inicialização do SQLAlchemy
db = SQLAlchemy(model_class=Base)

# Vincula a aplicação
db.init_app(app)

# Definição de um modelo de exemplo
class User(Base): # Pode usar db.Model ou somente Base
    __tablename__ = 'users'
    # Lembrar de importar Mapped e mapped_column 
    id: Mapped[int] = mapped_column(primary_key=True)
    name: Mapped[str]
    email: Mapped[str] = mapped_column(unique=True)

    # Retorna o nome do usuário
    def __repr__(self):
        return f'<Usuario {self.nome}>'

@app.route('/')
def home():
    return "Bem-vindo!"

if __name__ == '__main__':
    db.create_all()  # Cria as tabelas no banco de dados
    app.run(debug=True)
```
## **3. Consultas:**
- ### **select:**
```py
# Seleciona todos os users da tabela User
users = db.session.execute(db.select(User)).scalars()
```
- ### **Insert:**
```py
# Insere o Nome e o Email de um user na tabela User
user = User('Eduardo', 'eduardo@eduardo')
db.session.add(user)
db.session.commit() # Necessário para enviar os dados pro banco
```
- ### **Delete:**
```py
# Deleta um user do banco
user = db.get_or_404(User, id) # Busca o user por id e se não existir retorna um erro 404
db.session.delete(user)
db.session.commit ()
```
- ### **Update:**
```py
# Atualiza informações de um user
user = db.get_or_404(User,id) # pega o user pelo id
user.name = 'jose' # Atualiza o nome do user (Pode ser utilizado para atualizar qualquer campo da tabela do banco de dados)
db.session.commit()
```
- ### **Select + Where:**
```py
users = db.session.execute(db.select(User).where(User.name.contains(nome))).scalars() # Seleciona um user que tenha o nome que foi especifícado no where
```
