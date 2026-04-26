# Producto
Funciones y triggers para la base de datos Lawfirm

# SCRIPT de la DATABASE lawfirm
CREATE DATABASE Lawfirm;
USE Lawfirm;

-- =============================================
-- tablas de catálogo
-- =============================================

CREATE TABLE tipos_cliente (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Tipo_cliente VARCHAR(20)
);

CREATE TABLE sectores_actividad (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Sector VARCHAR(21)
);

CREATE TABLE especialidades (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Especialidad VARCHAR(20)
);

CREATE TABLE tipos_caso (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Tipo_caso VARCHAR(20)
);

CREATE TABLE ramas_derecho (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Rama_derecho VARCHAR(20)
);

CREATE TABLE estados_caso (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Estado VARCHAR(20)
);

CREATE TABLE tipos_actuacion (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Tipo_actuacion VARCHAR(20)
);

CREATE TABLE tipos_documento (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Descripcion VARCHAR(20)
);

CREATE TABLE estados_documento (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Estado VARCHAR(20)
);

CREATE TABLE tipos_audiencia (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Tipo_audiencia VARCHAR(20)
);

CREATE TABLE estados_pago (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Estado_pago VARCHAR(20)
);

CREATE TABLE tipos_material (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Tipo VARCHAR(25) NOT NULL
);

CREATE TABLE formatos_material (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Formato VARCHAR(20) NOT NULL
);

-- =============================================
-- tablas principales
-- =============================================

CREATE TABLE clientes (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Codigo VARCHAR(20),
	ID_tipo_cliente INT,
	Nombre VARCHAR(20),
	Documento VARCHAR(20),
	Direccion VARCHAR(45),
	Telefono VARCHAR(15),
	Email VARCHAR(50),
	Fecha_primer_contacto DATE,
	Referido_por VARCHAR(30),
	ID_sector INT,
	Clasificacion VARCHAR(40),
	FOREIGN KEY (ID_tipo_cliente) REFERENCES tipos_cliente(ID),
	FOREIGN KEY (ID_sector) REFERENCES sectores_actividad(ID)
);

CREATE TABLE abogados (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Colegiatura VARCHAR(30),
	Nombres VARCHAR(40),
	Apellidos VARCHAR(40),
	ID_especialidad INT,
	Años_experiencia INT,
	Formacion TEXT NOT NULL,
	Tarifa_hora DECIMAL(10,2),
	Disponibilidad VARCHAR(30),
	FOREIGN KEY (ID_especialidad) REFERENCES especialidades(ID)
);

CREATE TABLE casos (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Titulo VARCHAR(30),
	ID_tipo_caso INT,
	ID_rama_derecho INT,
	Fecha_apertura DATE,
	ID_cliente INT,
	Contraparte VARCHAR(50),
	Juzgado VARCHAR(50),
	Expediente_externo VARCHAR(33),
	ID_abogado_principal INT,
	ID_estado INT,
	Fecha_estimada DATE,
	FOREIGN KEY (ID_tipo_caso) REFERENCES tipos_caso(ID),
	FOREIGN KEY (ID_rama_derecho) REFERENCES ramas_derecho(ID),
	FOREIGN KEY (ID_cliente) REFERENCES clientes(ID),
	FOREIGN KEY (ID_estado) REFERENCES estados_caso(ID),
	FOREIGN KEY (ID_abogado_principal) REFERENCES abogados(ID)
);

-- on delete cascade: las actuaciones pertenecen al caso
CREATE TABLE actuaciones (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_caso INT,
	ID_tipo_actuacion INT,
	Fecha DATETIME,
	Lugar VARCHAR(30),
	Descripcion TEXT,
	Resultado TEXT,
	Siguiente_paso TEXT,
	Fecha_limite DATE,
	FOREIGN KEY (ID_caso) REFERENCES casos(ID) ON DELETE CASCADE,
	FOREIGN KEY (ID_tipo_actuacion) REFERENCES tipos_actuacion(ID)
);

CREATE TABLE documentos (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_tipo_documento INT,
	ID_caso INT,
	Titulo VARCHAR(150),
	Autor VARCHAR(100),
	Fecha_creacion DATE,
	Version_documento VARCHAR(20),
	ID_estado INT,
	Contenido TEXT,
	Confidencialidad VARCHAR(50),
	FOREIGN KEY (ID_tipo_documento) REFERENCES tipos_documento(ID),
	FOREIGN KEY (ID_estado) REFERENCES estados_documento(ID),
	FOREIGN KEY (ID_caso) REFERENCES casos(ID)
);

-- on delete cascade: las audiencias pertenecen al caso
CREATE TABLE audiencias (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_tipo_audiencia INT,
	ID_caso INT,
	Fecha DATETIME,
	Duracion INT,
	Lugar VARCHAR(30),
	Proposito TEXT,
	Resultado_esperado TEXT,
	Resultado_real TEXT,
	FOREIGN KEY (ID_tipo_audiencia) REFERENCES tipos_audiencia(ID),
	FOREIGN KEY (ID_caso) REFERENCES casos(ID) ON DELETE CASCADE
);

CREATE TABLE facturas (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Fecha DATE,
	ID_cliente INT,
	Gastos_reembolsables DECIMAL(10,2) DEFAULT 0,
	Periodo VARCHAR(25),
	Subtotal DECIMAL(10,2),
	Impuestos DECIMAL(10,2),
	Total DECIMAL(10,2),
	ID_estado INT,
	FOREIGN KEY (ID_cliente) REFERENCES clientes(ID),
	FOREIGN KEY (ID_estado) REFERENCES estados_pago(ID)
);

CREATE TABLE precedentes (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Jurisdiccion VARCHAR(100),
	Instancia VARCHAR(100),
	Fecha DATE,
	Tema VARCHAR(150),
	Resumen TEXT,
	Principios TEXT,
	Aplicabilidad TEXT,
	Texto TEXT
);

CREATE TABLE materiales_biblioteca (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_tipo_material INT,
	Titulo VARCHAR(55) NOT NULL,
	Autor VARCHAR(20),
	Editorial VARCHAR(20),
	Año INT,
	Materia VARCHAR(20),
	Ubicacion VARCHAR(20),
	ID_formato INT,
	Disponibilidad BOOLEAN DEFAULT TRUE,
	FOREIGN KEY (ID_formato) REFERENCES formatos_material(ID),
	FOREIGN KEY (ID_tipo_material) REFERENCES tipos_material(ID)
);

CREATE TABLE conocimientos (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	Area_legal VARCHAR(20),
	Subtema VARCHAR(20),
	Descripcion TEXT,
	Legislacion_aplicable TEXT,
	Jurisprudencia TEXT,
	Doctrina TEXT
);

-- =============================================
-- tablas intermedias
-- =============================================

CREATE TABLE idiomas_abogado (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_abogado INT,
	Idioma VARCHAR(50),
	FOREIGN KEY (ID_abogado) REFERENCES abogados(ID) ON DELETE CASCADE
);

CREATE TABLE caso_abogado (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_caso INT NOT NULL,
	ID_abogado INT NOT NULL,
	Rol VARCHAR(20),
	FOREIGN KEY (ID_caso) REFERENCES casos(ID) ON DELETE CASCADE,
	FOREIGN KEY (ID_abogado) REFERENCES abogados(ID) ON DELETE CASCADE
);

CREATE TABLE participantes_audiencia (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_audiencia INT,
	Nombre VARCHAR(40),
	Tipo VARCHAR(20),
	FOREIGN KEY (ID_audiencia) REFERENCES audiencias(ID) ON DELETE CASCADE
);

CREATE TABLE factura_caso (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_factura INT,
	ID_caso INT,
	FOREIGN KEY (ID_factura) REFERENCES facturas(ID) ON DELETE CASCADE,
	FOREIGN KEY (ID_caso) REFERENCES casos(ID) ON DELETE CASCADE
);

CREATE TABLE factura_detalle (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_factura INT,
	ID_abogado INT,
	Descripcion TEXT,
	Horas DECIMAL(5,2),
	Tarifa DECIMAL(10,2),
	FOREIGN KEY (ID_factura) REFERENCES facturas(ID) ON DELETE CASCADE,
	FOREIGN KEY (ID_abogado) REFERENCES abogados(ID)
);

CREATE TABLE actuacion_documento (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_actuacion INT,
	ID_documento INT,
	FOREIGN KEY (ID_actuacion) REFERENCES actuaciones(ID) ON DELETE CASCADE,
	FOREIGN KEY (ID_documento) REFERENCES documentos(ID) ON DELETE CASCADE
);

CREATE TABLE destinatario_documento (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_documento INT,
	ID_abogado INT,
	Destinatario VARCHAR(50),
	Tipo VARCHAR(20),
	FOREIGN KEY (ID_documento) REFERENCES documentos(ID) ON DELETE CASCADE,
	FOREIGN KEY (ID_abogado) REFERENCES abogados(ID)
);

CREATE TABLE material_prestamo (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_material INT,
	Fecha_prestamo DATE,
	Fecha_devolucion DATE,
	FOREIGN KEY (ID_material) REFERENCES materiales_biblioteca(ID) ON DELETE CASCADE
);

CREATE TABLE conocimiento_experto (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_conocimiento INT,
	ID_abogado INT,
	FOREIGN KEY (ID_conocimiento) REFERENCES conocimientos(ID) ON DELETE CASCADE,
	FOREIGN KEY (ID_abogado) REFERENCES abogados(ID) ON DELETE CASCADE
);

CREATE TABLE conocimiento_documento (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_conocimiento INT,
	ID_documento INT,
	FOREIGN KEY (ID_conocimiento) REFERENCES conocimientos(ID) ON DELETE CASCADE,
	FOREIGN KEY (ID_documento) REFERENCES documentos(ID) ON DELETE CASCADE
);

CREATE TABLE conocimiento_precedente (
	ID INT AUTO_INCREMENT PRIMARY KEY,
	ID_conocimiento INT NOT NULL,
	ID_precedente INT NOT NULL,
	FOREIGN KEY (ID_conocimiento) REFERENCES conocimientos(ID) ON DELETE CASCADE,
	FOREIGN KEY (ID_precedente) REFERENCES precedentes(ID) ON DELETE CASCADE
);

-- =============================================
-- insertar registros de prueba
-- =============================================

INSERT INTO especialidades (Especialidad) VALUES ('Civil'), ('Penal'), ('Laboral'), ('Tributario'), ('Mercantil');
INSERT INTO estados_caso (Estado) VALUES ('Activo'), ('Cerrado'), ('En espera'), ('Ganado'), ('Perdido'), ('Conciliado');

INSERT INTO abogados (Colegiatura, Nombres, Apellidos, ID_especialidad, Años_experiencia, Formacion, Disponibilidad, Tarifa_hora)
VALUES
('001', 'Laura', 'Gómez', 2, 8, 'Universidad de los Andes, Especialización Penal', 'No disponible', 200.00),
('002', 'Pedro', 'Ramírez', 3, 2, 'Universidad EAFIT, Derecho Laboral', 'Disponible', 90.00),
('003', 'Ana', 'Torres', 4, 10, 'Universidad Javeriana, Maestría Tributaria', 'Disponible', 250.00),
('004', 'Luis', 'Herrera', 5, 1, 'Universidad Nacional, Derecho Mercantil', 'No disponible', 80.00);

INSERT INTO tipos_cliente (Tipo_cliente) VALUES ('Persona Natural'), ('Empresa');
INSERT INTO sectores_actividad (Sector) VALUES ('Tecnología'), ('Construcción'), ('Salud');

INSERT INTO clientes (Codigo, ID_tipo_cliente, Nombre, Documento, Telefono, Email, Fecha_primer_contacto, ID_sector)
VALUES
('001', 1, 'Juan Pérez', '1234567', '3001234567', 'juan@email.com', '2023-03-15', NULL),
('002', 2, 'Constructora', '900123456', '3109876543', 'constructora@empresa.com', '2022-08-01', 2),
('003', 1, 'María López', '9876543', '3207654321', NULL, '2024-01-10', NULL);

INSERT INTO tipos_caso (Tipo_caso) VALUES ('Contencioso'), ('Consultivo');
INSERT INTO ramas_derecho (Rama_derecho) VALUES ('Civil'), ('Penal'), ('Laboral');

INSERT INTO casos (Titulo, ID_tipo_caso, ID_rama_derecho, Fecha_apertura, ID_cliente, Contraparte, Juzgado, Expediente_externo, ID_abogado_principal, ID_estado, Fecha_estimada)
VALUES
('Garcia vs Lopez', 1, 1, '2023-05-10', 1, 'Lopez S.A.', 'Juzgado 1 Civil', 'EXP-2023-001', 1, 1, '2024-06-30'),
('Despido injusto', 1, 3, '2024-02-20', 3, 'Empresa ABC', 'Juzgado Laboral', 'EXP-2024-002', 3, 1, NULL),
('Consulta tributaria', 2, 1, '2022-11-01', 2, NULL, NULL, 'EXP-2022-003', 4, 3, '2023-03-01'),
('Fraude financiero', 1, 2, '2023-09-15', 1, 'Desconocido', 'Juzgado Penal', 'EXP-2023-004', 2, 3, '2025-01-01');

INSERT INTO tipos_audiencia (Tipo_audiencia) VALUES ('Inicial'), ('Pruebas'), ('Alegatos'), ('Sentencia');

INSERT INTO audiencias (ID_tipo_audiencia, ID_caso, Fecha, Duracion, Lugar, Proposito, Resultado_esperado, Resultado_real)
VALUES
(1, 1, '2024-04-05 09:00:00', 60, 'Juzgado 1 Civil', 'Apertura del caso Garcia vs Lopez', 'Admisión de pruebas', 'Pruebas admitidas'),
(2, 2, '2024-04-12 10:30:00', 90, 'Juzgado Laboral', 'Presentación de pruebas despido', 'Fallo a favor', NULL),
(3, 3, '2024-04-20 14:00:00', 45, 'Juzgado Civil', 'Alegatos finales tributaria', 'Acuerdo entre partes', NULL),
(1, 4, '2024-03-10 08:00:00', 60, 'Juzgado Penal', 'Audiencia inicial fraude', 'Imputación de cargos', 'Cargos imputados');

INSERT INTO tipos_documento (Descripcion) VALUES ('Contrato'), ('Demanda'), ('Recurso'), ('Informe'), ('Dictamen');
INSERT INTO estados_documento (Estado) VALUES ('Borrador'), ('Revision'), ('Final');

INSERT INTO documentos (ID_tipo_documento, ID_caso, Titulo, Autor, Fecha_creacion, Version_documento, ID_estado, Contenido, Confidencialidad)
VALUES
(1, 1, 'Contrato Garcia Lopez', 'Carlos Mendoza', '2023-05-15', 'v1.0', 3, 'Contrato de representación legal', 'Alta'),
(2, 2, 'Demanda despido injusto', 'Pedro Ramírez', '2024-02-25', 'v1.0', 3, 'Demanda por despido sin justa causa', 'Media'),
(3, 1, 'Recurso apelación', 'Carlos Mendoza', '2023-08-10', 'v2.0', 2, 'Recurso de apelación sentencia', 'Alta'),
(4, 3, 'Informe tributario', 'Ana Torres', '2022-11-15', 'v1.0', 3, 'Informe análisis tributario', 'Baja'),
(5, 4, 'Dictamen fraude', 'Laura Gómez', '2023-10-01', 'v1.0', 1, 'Dictamen sobre fraude financiero', 'Alta');

INSERT INTO precedentes (Jurisdiccion, Instancia, Fecha, Tema, Resumen, Principios, Aplicabilidad, Texto)
VALUES
('Colombia', 'Corte Suprema', '2020-03-15', 'Propiedad Intelectual', 'Caso sobre propiedad intelectual en obras digitales', 'Protección de obras originales', 'Alta', 'Texto completo del precedente...'),
('Colombia', 'Consejo de Estado', '2019-07-22', 'Derecho de Autor', 'Vulneración del derecho de autor en plataformas digitales', 'Titularidad y explotación de obras', 'Alta', 'Texto completo del precedente...'),
('Colombia', 'Tribunal Superior', '2021-11-10', 'Marcas', 'Uso indebido de marca registrada', 'Protección de signos distintivos', 'Media', 'Texto completo del precedente...'),
('Colombia', 'Corte Constitucional', '2022-05-30', 'Propiedad Intelectual', 'Derecho de autor sobre software', 'Protección del software como obra literaria', 'Alta', 'Texto completo del precedente...');

INSERT INTO estados_pago (Estado_pago) VALUES ('Pendiente'), ('Pagado'), ('Vencido');

INSERT INTO tipos_actuacion (Tipo_actuacion)
VALUES ('Audiencia'), ('Diligencia'), ('Notificacion'), ('Recurso'), ('Alegato');

-- =============================================
-- índices
-- =============================================

CREATE INDEX idx_casos_cliente ON casos (ID_cliente);
CREATE INDEX idx_casos_estado ON casos (ID_estado);
CREATE INDEX idx_casos_cliente_estado ON casos (ID_cliente, ID_estado);
CREATE INDEX idx_fact_cliente_estado ON facturas (ID_cliente, ID_estado);
CREATE UNIQUE INDEX idx_u_caso_abogado ON caso_abogado (ID_caso, ID_abogado);
CREATE UNIQUE INDEX idx_u_factura_caso ON factura_caso (ID_factura, ID_caso);

-- =============================================
-- actualizaciones de estado
-- =============================================

UPDATE casos SET ID_estado = 4 WHERE ID = 1; -- García vs López = Ganado
UPDATE casos SET ID_estado = 5 WHERE ID = 3; -- Consulta tributaria = Perdido
UPDATE casos SET ID_estado = 6 WHERE ID = 4; -- Fraude financiero = Conciliado
