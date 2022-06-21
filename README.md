# CarInfoViewer
Car information Viewer with FastAPI, Jinja and Bootstrap

# Pydantic Model
    - Field:
        - ...
        - gt: great than
        - ge: great equal
        - lt: less than

# Config Database (OAuth2 course way)

Never forget SQLAlchemy 'cause here it will be our ORM. First we should create the database models in corresponly file. 

## Database file (Created, read and added)

```
from sqlalchemy import create_engine, engine
from sqlalchemy.ext.declarative import declarative_base
from sqlalchemy.orm import sessionmaker
from config import SQLALCHEMY_DATABSE_URL

engine = create_engine(
    SQLALCHEMY_DATABSE_URL, connect_args = {"check_same_thread": False}
)

SessionLocal = sessionmaker(autocommit = False, autoflush = False, bind = engine.bind)

Base = declarative_base()

def get_db():
    db = SessionLocal()
    try:
        yield db
    finally:
        db.close()
```

## Models example

```
from sqlalchemy import Column, Integer, String, Float, Boolean, ForeignKey
from database import Base
from sqlalchemy.orm import relationship

class Car(Base):
    __tablename__ = "cars"
    id = Column(Integer, primary_key=True, index=True)
    model = Column(String)
    year = Column(Integer)
    price = Column(Float)
    autonomus = Column(Boolean)
    make_id = Column(Integer, ForeignKey("makes.id"))
    engine_id = Column(Integer, ForeignKey("engines.id"))
    sold_id = Column(Integer, ForeignKey("solds.id"))
    
    # relationships
    make = relationship("Make", back_populates="cars")
    engine = relationship("Engine", back_populates="cars")
    sold = relationship("Sold", back_populates="cars")
    
class Sold(Base):
    __tablename__ = "solds"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True, unique=True)
    cars = relationship("Car", back_populates="solds")

class Make(Base):
    __tablename__ = "makes"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True, unique=True)
    cars = relationship("Car", back_populates="makes")

class Engine(Base):
    __tablename__ = "engines"
    id = Column(Integer, primary_key=True, index=True)
    name = Column(String, index=True, unique=True)
    cars = relationship("Car", back_populates="engines")
```

## Main file modifications

```
from fastapi import FastAPI
from config import config
from routers import car
from database import engine
from models import Base 

app = FastAPI(**config)

# Router
app.include_router(car.router)

# Create table
Base.metadata.create_all(engine)
```

## Pydantic relationship

# Instalation

## Modules
    black
    creditkit-tools
    fastapi
    pydantic
    sqlalchemy
    uvicorn
