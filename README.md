"# examenfinatw1"  
CREATE DATABASE EscuelaDB;
GO

USE EscuelaDB;
GO

CREATE TABLE Estudiantes (
    id_estudiante INT PRIMARY KEY IDENTITY(1,1),
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    email VARCHAR(150) UNIQUE,
    fecha_nacimiento DATE,
    estado VARCHAR(20) DEFAULT 'Activo',
    CONSTRAINT chk_estado_estudiante CHECK (estado IN ('Activo', 'Inactivo'))
);

CREATE TABLE Cursos (
    id_curso INT PRIMARY KEY IDENTITY(1,1),
    nombre_curso VARCHAR(100) NOT NULL,
    creditos INT NOT NULL,
    cupo_maximo INT,
    CONSTRAINT chk_creditos CHECK (creditos > 0),
    CONSTRAINT chk_cupo CHECK (cupo_maximo > 0)
);

CREATE TABLE Inscripciones (
    id_inscripcion INT PRIMARY KEY IDENTITY(1,1),
    id_estudiante INT,
    id_curso INT,
    fecha_inscripcion DATE DEFAULT GETDATE(),
    estado VARCHAR(20) DEFAULT 'Inscrito',
    CONSTRAINT fk_estudiante FOREIGN KEY (id_estudiante)
        REFERENCES Estudiantes(id_estudiante),
    CONSTRAINT fk_curso FOREIGN KEY (id_curso)
        REFERENCES Cursos(id_curso),
    CONSTRAINT chk_estado_inscripcion CHECK (estado IN ('Inscrito', 'Retirado'))
);

INSERT INTO Estudiantes (nombre, apellido, email, fecha_nacimiento) VALUES
('Juan', 'Perez', 'juan@gmail.com', '2000-05-10'),
('Maria', 'Lopez', 'maria@gmail.com', '1999-08-20'),
('Carlos', 'Gomez', 'carlos@gmail.com', '2001-03-15');

INSERT INTO Cursos (nombre_curso, creditos, cupo_maximo) VALUES
('Base de Datos', 5, 30),
('Programación', 4, 25),
('Redes', 3, 20);

INSERT INTO Inscripciones (id_estudiante, id_curso) VALUES
(1, 1),
(2, 2),
(3, 3),
(1, 2);

CREATE INDEX idx_email_estudiante
ON Estudiantes(email);

CREATE VIEW Vista_Inscripciones AS
SELECT 
    e.nombre,
    e.apellido,
    c.nombre_curso,
    i.fecha_inscripcion,
    i.estado
FROM Inscripciones i
JOIN Estudiantes e ON i.id_estudiante = e.id_estudiante
JOIN Cursos c ON i.id_curso = c.id_curso;

SELECT nombre, apellido
FROM Estudiantes
WHERE id_estudiante IN (
    SELECT id_estudiante
    FROM Inscripciones
    GROUP BY id_estudiante
    HAVING COUNT(id_curso) > 1
);

SELECT * FROM Vista_Inscripciones;
