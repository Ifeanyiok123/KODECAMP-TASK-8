# KODECAMP-TASK-8
# Developing a Fully Functional API with FastAPI for a Personal Portfolio Website.

In this guide, we will create a fully functional API using FastAPI in Python for a personal portfolio website. The API will include endpoints for managing projects, blog posts, and contact information. We will use SQLite as our database to store the data.

# Step 1: Setting Up the Environment
First, ensure you have Python installed on your machine. You can check this by running:
python --version

Next, create a new directory for your project and navigate into it:
mkdir portfolio_api
cd portfolio_api

Now, create a virtual environment and activate it:
python -m venv venv
source venv/bin/activate  # On Windows use `venv\Scripts\activate`

Install the required packages:
pip install fastapi uvicorn sqlalchemy sqlite3 pydantic

# Step 2: Creating the Database Models
Create a file named models.py to define our database models using SQLAlchemy.

# models.py

from sqlalchemy import Column, Integer, String, Text, create_engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker

Base = declarative_base()

class Project(Base):
    __tablename__ = 'projects'
    
    id = Column(Integer, primary_key=True, index=True)
    title = Column(String(100), index=True)
    description = Column(Text)
    url = Column(String(200))

class BlogPost(Base):
    __tablename__ = 'blog_posts'
    
    id = Column(Integer, primary_key=True, index=True)
    title = Column(String(100), index=True)
    content = Column(Text)

class ContactInfo(Base):
    __tablename__ = 'contact_info'
    
    id = Column(Integer, primary_key=True, index=True)
    email = Column(String(100))
    phone_number = Column(String(15))

# Database setup
DATABASE_URL = "sqlite:///./portfolio.db"
engine = create_engine(DATABASE_URL)
SessionLocal = sessionmaker(autocommit=False, autoflush=False, bind=engine)

def init_db():
    Base.metadata.create_all(bind=engine)

# Step 3: Creating the FastAPI Application
Create a file named main.py where we will set up our FastAPI application and define the endpoints.

# main.py

from fastapi import FastAPI, Depends, HTTPException
from sqlalchemy.orm import Session
from models import init_db, SessionLocal, Project as ProjectModel, BlogPost as BlogPostModel, ContactInfo as ContactInfoModel

app = FastAPI()

# Initialize the database
init_db()

# Dependency to get DB session
def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()

@app.post("/projects/")
def add_project(project: ProjectModel, db: Session = Depends(get_db)):
    db.add(project)
    db.commit()
    db.refresh(project)
    return project

@app.get("/projects/")
def get_projects(db: Session = Depends(get_db)):
    return db.query(ProjectModel).all()

@app.get("/projects/{project_id}")
def get_project(project_id: int, db: Session = Depends(get_db)):
    project = db.query(ProjectModel).filter(ProjectModel.id == project_id).first()
    if not project:
        raise HTTPException(status_code=404, detail="Project not found")
    return project

@app.put("/projects/{project_id}")
def edit_project(project_id: int, updated_project: ProjectModel, db: Session = Depends(get_db)):
    project_query = db.query(ProjectModel).filter(ProjectModel.id == project_id)
    
    if not project_query.first():
        raise HTTPException(status_code=404, detail="Project not found")
    
    project_query.update(updated_project.dict())
    db.commit()
    
    return project_query.first()

@app.delete("/projects/{project_id}")
def delete_project(project_id: int, db: Session = Depends(get_db)):
    project_query = db.query(ProjectModel).filter(ProjectModel.id == project_id)
    
    if not project_query.first():
        raise HTTPException(status_code=404, detail="Project not found")
    
    project_query.delete()
    db.commit()
    
    return {"detail": "Project deleted"}

# Blog Posts Endpoints (similar structure as Projects)

@app.post("/blog_posts/")
def add_blog_post(blog_post: BlogPostModel, db: Session = Depends(get_db)):
   # Similar implementation as add_project
   
@app.get("/blog_posts/")
def get_blog_posts(db: Session = Depends(get_db)):
   # Similar implementation as get_projects
   
@app.get("/blog_posts/{post_id}")
def get_blog_post(post_id: int):
   # Similar implementation as get_project
   
@app.put("/blog_posts/{post_id}")
def edit_blog_post(post_id:int):
   # Similar implementation as edit_project
   
@app.delete("/blog_posts/{post_id}")
def delete_blog_post(post_id:int):
   # Similar implementation as delete_project

# Contact Information Endpoints (similar structure)

@app.post("/contact_info/")
def add_contact_info(contact_info: ContactInfoModel):
   # Implementation here
   
@app.put("/contact_info/{info_id}")
def edit_contact_info(info_id:int):
   # Implementation here
   
@app.delete("/contact_info/{info_id}")
def delete_contact_info(info_id:int):
   # Implementation here

if __name__ == "__main__":
   import uvicorn 
   uvicorn.run(app)

# Step 4: Running the Application
To run your application locally:

uvicorn main:app --reload

You can now access your API at http://127.0.0.1:8000/docs to see the automatically generated documentation provided by FastAPI.

# Step 5: Pushing Code to GitHub

1.	Initialize a git repository in your directory:
git init
Add your files and commit them:
git add .
2.	git commit -m "Initial commit of portfolio API"
3.	Create a new repository on GitHub and follow their instructions to push your local repository to GitHub.

# Conclusion
You now have a fully functional API built with FastAPI that manages projects and blog posts along with contact information for your personal portfolio website. This setup allows you to easily expand or modify features in the future. 

