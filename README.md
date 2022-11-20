DROP DATABASE IF EXISTS constructora;

CREATE DATABASE constructora;

USE constructora;

-- SE CREAN LAS TABLAS PADRE clientes; proveedores y arquitectos –

DROP TABLE IF EXISTS clientes;

CREATE TABLE clientes (

clientes_id INT NOT NULL AUTO_INCREMENT,

nombre VARCHAR(50),

direccion VARCHAR(50),

email VARCHAR(70),

telefono FLOAT,

presup_dol DECIMAL(8,0),

PRIMARY KEY (clientes_id)

);
insert into clientes (clientes_id, nombre, direccion, email, telefono, presup_dol)
values 
(1, "mariana tello", "Donoso Cortez 138 Agua de Oro", "mariana.tello@incluit.com", 3525481224, 26000),
(2, "Josefina Ferrraro", "San Martin 138 Salsipuedes", "josefina022@hotmail.com", 3525481552, 25500),
(3, "Maximo Diaz", "Mariano Moreno 138 Rio Ceballos", "maxpower@gmail.com", 3525721218, 52000);

DROP TABLE IF EXISTS proveedores;

CREATE TABLE proveedores (

proveedores_id INT NOT NULL AUTO_INCREMENT,

nombre VARCHAR(60),

telefono VARCHAR(20),

direccion varchar(60),

PRIMARY KEY (proveedores_id)

) ;

INSERT INTO proveedores (proveedores_id, nombre, telefono, direccion)
Values
(1, "Hiperconstruccion", 3514747200, "Fray Luis Beltran y Manuel Cardenosa"),
(2, "mis ladrillos cba", 3514846789, "Avenida Colon 5225"),
(3, "Corralon San Miguel", 3543489708, "Tte. Gral. Donato Alvarez 8509 Cordoba");


DROP TABLE IF EXISTS arquitectos;

CREATE TABLE arquitectos (

arquitectos_id INT NOT NULL AUTO_INCREMENT,

nombre VARCHAR(50),

direccion VARCHAR(50),

email VARCHAR(70),

telefono VARCHAR(20),

PRIMARY KEY (arquitectos_id)

) ;

Insert into arquitectos (arquitectos_id, nombre, direccion, email, telefono)
VALUES (1, "jorge diaz", "De los montes 520", "jorge_diaz_20@hotmail.com", 3525481224);

-- SE CREAN LAS TABLAS HIJAS materiales(SE RELACIONA CON proveedores) y eq_trabajo(SE RELACIONA CON arquitectos). –

DROP TABLE IF EXISTS materiales;

CREATE TABLE compra_materiales (

compra_materiales_id INT NOT NULL AUTO_INCREMENT,

material VARCHAR(50),
costo DECIMAL(8,0),
fecha DATE,

fk_proveedores_id INT NOT NULL,

PRIMARY KEY (compra_materiales_id),

FOREIGN KEY(fk_proveedores_id) REFERENCES proveedores(proveedores_id)

) ;

INSERT INTO compra_materiales (compra_materiales_id, material, costo, fecha, fk_proveedores_id)
VALUES
(1, "ladrillos, cemento, hierro", 100000, 20220812, 1),
(2, "hierro, pintura, vigas", 1001050, 20220816, 1),
(3, "cables, caja de luz, lamapara", 10050, 20220818, 3),
(4, "p de latex, enduido, brochas", 15000, 20220819, 2),
(5, "maq para pintar, pintura, ag raz", 1000050, 20220822, 2),
(6, "ladrillos, cemento, hierro", 1000050, 20220825, 3);

DROP TABLE IF EXISTS eq_trabajo;

CREATE TABLE eq_trabajo (

eq_trabajo_id INT NOT NULL AUTO_INCREMENT,

fk_arquitectos_id INT NOT NULL,

nombre VARCHAR(50),

cuit_cuil float,

rubro_jerarquia VARCHAR(50),

nro_poliza float,

telefono float,

PRIMARY KEY (eq_trabajo_id),

FOREIGN KEY (fk_arquitectos_id) REFERENCES arquitectos(arquitectos_id)

) ;

INSERT INTO eq_trabajo (eq_trabajo_id, fk_arquitectos_id, nombre, cuit_cuil, rubro_jerarquia, nro_poliza,telefono)
VALUES 
(1, 1, "Precision SA", 30175244419, "Instalaciones", 10022000156, 3525485649),
(2, 1, "Juan Perez", 20338526939, "Jefe de obra", 10000000156, 3525428568),
(3, 1, "Lucas Corbacho", 20325699649, "Albanil", 35200253616, 35115481242),
(4, 1, "Plomeria Gomez", 32525559870, "Plomeria", 10033000156, 3543568923),
(5, 1, "Gabriel Mauricio", 20222583699, "Albanil",	36589562374, 3525487845),
(6, 1, "Pintureria Ledesma", 30326598320, "Pintura", 10006600156,	3525421321),
(7, 1, "Estefano Diaz",  20383735219, "Arq de obra", 56032875421,	35132165984),
(8, 1, "Jomaga", 30125236250, "Parquizacion", 10005600156, 35112457896),
(9, 1, "Joaquin Gutierrez", 20338715269, "Encofrador", 35265568998, 3525481058),
(10, 1, "Jose Martinez", 20223563599, "Soldador", 63289788756, 3525527254),
(11, 1, "Maximo Romero", 20201541540, "Jefe de obra", 10005653265, 3525647093);



-- YA SE PUEDE CREAR LA TABLA obras, ESTA TIENE LAS REFERENCIAS DE LA TABLA materiales y eq_trabajo -

DROP TABLE IF EXISTS obras;

CREATE TABLE obras(

id INT NOT NULL AUTO_INCREMENT,

fk_compra_materiales_id INT NOT NULL,

fk_eq_trabajo_id INT NOT NULL,

tipo VARCHAR(50),

ubicación varchar(50),

fecha_inicio_contrato DATE,

fecha_final_contrato DATE,

PRIMARY KEY (id),

FOREIGN KEY (fk_compra_materiales_id) REFERENCES compra_materiales(compra_materiales_id),

FOREIGN KEY (fk_eq_trabajo_id) REFERENCES eq_trabajo(eq_trabajo_id)

);

INSERT INTO obras (id, fk_compra_materiales_id, fk_eq_trabajo_id, tipo, ubicación, fecha_inicio_contrato, fecha_final_contrato)
VALUES 
(1, 2, 3, "llave en mano", "San Martin 520 Rio Ceballos",	20220325,	20221201),
(2, 3, 2, "solo plano relevamiento", "Tucuman 1293 Salsipuedes",	20220516,	20221130),
(3, 1, 4, "plano y construccion", "De las cascadas 0 Agua de Oro",	20220615,	20221130);

/* ATENDIENDO LA REGLA DEL NEGOCIO, UN CLIENTE PUEDE CONTRATAR VARIAS OBRAS,

ENTONCES SE DA UNA RELACION DE MUCHOS A MUCHOS POR LO QUESE GENERA UNA TABLA INTERMEDIA */

DROP TABLE IF EXISTS clientes_obras;

CREATE TABLE clientes_obras(

clientes_obras_id INT NOT NULL AUTO_INCREMENT,

fk_clientes_id INT NOT NULL,

fk_obras_id INT NOT NULL,

PRIMARY KEY (clientes_obras_id),

FOREIGN KEY (fk_clientes_id) REFERENCES clientes(clientes_id),

FOREIGN KEY (fk_obras_id) REFERENCES obras(id)

);

insert into clientes_obras (clientes_obras_id, fk_clientes_id, fk_obras_id)
values 
(1, 1, 1),
(2, 2, 2),
(3, 3, 3);

DROP TABLE IF EXISTS pagos;

CREATE TABLE pagos(

pagos_id INT NOT NULL AUTO_INCREMENT,

fk_clientes_id INT NOT NULL,

fk_obras_id INT NOT NULL,

forma_pago VARCHAR(60),

modo_pago VARCHAR (60),

fecha_pago DATE,

PRIMARY KEY (pagos_id),

FOREIGN KEY (fk_clientes_id) REFERENCES clientes(clientes_id),

FOREIGN KEY (fk_obras_id) REFERENCES obras(id)

);

INSERT INTO pagos (pagos_id, fk_clientes_id, fk_obras_id, forma_pago, modo_pago, fecha_pago)
Values
(1,	1,1, "porcentaje de entrega", "transferencia", 20221020),
(2,	2,2, "semanal",	"echeq", 20221020),
(3,	3,3, "quincena", "efectivo", 20221020);


CREATE TABLE logs_clientes (
  log_clientes_id INT NOT NULL AUTO_INCREMENT,
  nombre varchar(50),
  direccion varchar(50),
  email varchar(70),
  telefono float,
  presup_dol decimal(8,0),
  PRIMARY KEY (log_clientes_id)
);

CREATE TABLE logs_eq_trabajo (
  logs_eq_trabajo_id int unsigned NOT NULL AUTO_INCREMENT,
  fecha_hora date NOT NULL,
  tipo varchar(45) NOT NULL,
  detalle varchar(145) NOT NULL,
  PRIMARY KEY (logs_eq_trabajo_id)
  );
  
  CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `constructora`.`datos_eq_agua_de_oro` AS select 1 AS `nombre`,1 AS `telefono`,1 AS `ubicación`
  CREATE ALGORITHM=UNDEFINED DEFINER=`root`@`localhost` SQL SECURITY DEFINER VIEW `constructora`.`obras_entregadas` AS select `c`.`nombre` AS `nombre`,`c`.`telefono` AS `telefono` from (`constructora`.`clientes` `c` join `constructora`.`obras` `o`) where (`o`.`fecha_final_contrato` < curdate())
  CREATE DEFINER=`root`@`localhost` PROCEDURE `actualiza_presup`(n_presup_dol INT, id INT)
UPDATE clientes SET presup_dol=n_presup_dol WHERE obras=_id
  --SP actualizar presupuesto de cliente
CREATE PROCEDURE actualiza_presup(n_presup_dol INT, id INT)
UPDATE clientes SET presup_dol=n_presup_dol WHERE obras=id

delimiter // 
create procedure eq_rubro_jerarquia (IN rubro_jerarquia varchar(50))
BEGIN
select nombre, rubro_jerarquia, eq_trabajo_id
From eq_trabajo
WHERE eq_rubro_jerarquia LIKE rubro_jerarquia;
END//

--triggerAFTER EN TABLA log_eq_trabajo


 delimiter // 
CREATE TRIGGER trigger_eq_trabajo

AFTER INSERT ON eq_trabajo

FOR EACH ROW

BEGIN

INSERT INTO log_eq_trabajo (logs_eq_trabajo_id, fecha_hora, tipo, detalle)

VALUES (1, 20220830-8.2, "mano de obra", "ingreso");

END; $$

--Triggerbefore en tabla log eq trabajo 

delimiter //
 create trigger before_eq_trabajo_insert
   before insert
   on eq_trabajo
   for each row
 begin
   update nombre, cuit_cuil, rubro_jerarquia, nro_poliza, telefono set eq_trabajo_id=new.eq_trabajo_id
     where new.eq_trabajo_id=eq_trabajo_id; 
 end //
 delimiter ;

--triggerAFTER en tabla log_clientes

DELIMITER $$
CREATE TRIGGER trigger_cliente
AFTER INSERT ON clientes
FOR EACH ROW
BEGIN 
   INSERT INTO log_clientes(log_clientes_id, nombre, direccion, email, telefono, presup_dol)
   VALUES (NEW.nombre, NEW.direccion, NEW.email, NEW.telefono, NEW.presup_dol, CURDATE());
END; $$


--triggerbefor log_clientes


delimiter //
 create trigger before_clientes_insert
   before insert
   on clientes
   for each row
 begin
   update nombre, direccion, email, telefono,presup_dol set clientes_id=new.clientes_id
     where new.clientes_id=clientes_id; 
 end //
 delimiter ;



-- views obras entregadas


DROP view IF EXISTS obras_entregadas; 

Create view obras_entregadas AS 
SELECT distinct c.nombre, o.fecha_final_contrato 
FROM clientes AS c, obras AS o 
Where o.fecha_final_contrato < curdate();


-- obra con mayor presupuesto

DROP VIEW IF EXISTS ubicación_mayor_presup;

CREATE VIEW ubicación_mayor_presup as
select o.ubicación, c.presup_dol, c.nombre
from obras as o, clientes as c, clientes_obras as co
inner join clientes on co.fk_clientes_id = clientes_id
inner join obras on co.fk_obras_id = id
having MAX(c.presup_dol);

-- colaboradores activos en agua de oro
CREATE VIEW datos_eq_agua_de_oro as 
SELECT distinct eq.nombre, eq.telefono, o.ubicación, o.tipo
FROM eq_trabajo as eq, obras as o 
inner join eq_trabajo on o.fk_eq_trabajo_id = eq_trabajo_id
where ubicación LIKE '% agua de oro'; 



DROP VIEW  IF EXISTS fecha_ubicación_finalizadas_2022;
CREATE VIEW fecha_ubicación_finalizadas_2022 as
SELECT distinct o.fecha_final_contrato, o.ubicación
  FROM obras as o, clientes_obras as co
WHERE o.fecha_final_contrato < 20221231;

-- dar tipo de obrasegun su nombre-- 

delimiter $$
create function dar_tipo_obra_por_cliente (p_nombre varchar(50))
returns char(50)
deterministic
BEGIN
DECLARE v_tipo char(50);
 SELECT o.tipo, c.nombre	
 into v_tipo
 From obras as o, clientes as c
 where nombre = p_nombre;

 RETURN v_tipo;
end $$


-- eq de trabajo activo por obra--

delimiter $$
CREATE FUNCTION eq_trabajo_activo_por_obra (p_obras_id int)
returns int
deterministic
BEGIN
DECLARE v_eq_trabajo_id int;
SELECT eq.eq_trabajo_id, o.id
into v_eq_trabajo_id
From eq_trabajo as eq, obras as o
Where  id = p_obras_id;

return v_eq_trabajo_id;
end $$


-- Este usuario ha sido creado y debería poseer habilitado solo la lectura de la base --

Create user 'a_juana'@'127.0.0.1' identified by 'Admin1234';


-- Se le otorga mediante la sentencia GRANT el permiso de lectura de nuestra base "constructora"--
grant select on constructora.* to 'a_juana'@'127.0.0.1';


-- Visualizamos los permisos que posee el usuario.

Show grants for 'a_juana'@'127.0.0.1';


-- Creamos al usuario jorge_a este usuario tiene la capacidad de lectura, 
modificación y de agregar datos a nuestra base--

CREATE USER 'arq_jorge_diaz'@127.0.0.1 identified by 'Admin1234';


-- le otorgamos los permisos de lectura, agregar y modificar datos de la base.


grant select, insert, update on constructora.* to 'arq_jorge_diaz'@127.0.0.1;


-- visualizamos los permisos del usuario

show grants for 'arq_jorge_diaz'@127.0.0.1;

-- stored provedure para actualizar el presupuesto del cliente

CREATE DEFINER=`root`@`localhost` PROCEDURE `actualiza_presup`(n_presup_dol INT, id INT)
UPDATE clientes SET presup_dol=n_presup_dol WHERE obras=_id

-- se desactiva el autoccomit

SET AUTOCOMMIT = 0;

-- Tabla 1 proveedores --

start transaction;
update proveedores

set 
telefono = 3525481254
where
proveedores_id = 2; 
commit;




-- TABLA 2 PAGOS --



Start transaction;
select * from pagos ORDER BY pagos_id desc;

INSERT INTO pagos values(4,2,2, 'quincenal', 'efectivo', 20221120);
INSERT INTO pagos values(6,3,3, 'Semanal', 'efectivo', 20221031);
INSERT INTO pagos values(7,1,1, 'mensual', 'transferencia', 20221122);

commit;
SET AUTOCOMMIT = 1;
