CREATE TABLE clienti (
    numero_cliente SERIAL PRIMARY KEY,
    nome VARCHAR(50),
    cognome VARCHAR(50),
    anno_di_nascita INTEGER,
    regione_residenza VARCHAR(50)
);

CREATE TABLE prodotti (
    id_prodotto SERIAL PRIMARY KEY,
    descrizione VARCHAR(255),
    in_produzione BOOLEAN,
    in_commercio BOOLEAN,
    data_attivazione DATE,
    data_disattivazione DATE
);

CREATE TABLE fornitori (
    numero_fornitore SERIAL PRIMARY KEY,
    denominazione VARCHAR(100),
    regione_residenza VARCHAR(50)
);

CREATE TABLE fatture (
    numero_fattura VARCHAR(50) PRIMARY KEY,
    tipologia VARCHAR(50),
    importo DECIMAL(10, 2),
    iva DECIMAL(5, 2),
    id_cliente INTEGER REFERENCES clienti(numero_cliente),
    data_fattura DATE,
    numero_fornitore INTEGER REFERENCES fornitori(numero_fornitore)
);

INSERT INTO clienti (nome, cognome, anno_di_nascita, regione_residenza) VALUES
('Mario', 'Rossi', 1985, 'Lombardia'),
('Laura', 'Bianchi', 1982, 'Toscana'),
('Giuseppe', 'Verdi', 1990, 'Veneto'),
('Anna', 'Neri', 1982, 'Lazio'),
('Mario', 'Biondi', 1975, 'Piemonte');

INSERT INTO prodotti (descrizione, in_produzione, in_commercio, data_attivazione, data_disattivazione) VALUES
('Laptop gaming', TRUE, TRUE, '2023-01-15', NULL),
('Smartphone X', TRUE, TRUE, '2022-10-01', NULL),
('Webcam HD', FALSE, TRUE, '2021-05-20', '2023-04-30');

INSERT INTO fornitori (denominazione, regione_residenza) VALUES
('Tech Supply Srl', 'Emilia-Romagna'),
('Global Components Spa', 'Lombardia'),
('Elettronica Pro Snc', 'Campania');

INSERT INTO fatture (numero_fattura, tipologia, importo, iva, id_cliente, data_fattura, numero_fornitore) VALUES
('FATT001', 'Vendita', 1200.50, 22.00, 1, '2024-09-20', 1),
('FATT002', 'Servizio', 50.00, 22.00, 2, '2024-09-21', 2),
('FATT003', 'Vendita', 89.99, 22.00, 3, '2024-09-22', 3),
('FATT004', 'Vendita', 345.20, 10.00, 1, '2024-09-23', 1);

SELECT * FROM clienti WHERE nome = 'Mario';

SELECT nome, cognome FROM clienti WHERE anno_di_nascita = 1982;

SELECT numero_fattura FROM fatture WHERE iva = 20.00;

SELECT *
FROM prodotti
WHERE EXTRACT(YEAR FROM data_attivazione) = 2017
  AND (in_produzione = TRUE OR in_commercio = TRUE);

SELECT
  F.numero_fattura,
  F.importo,
  F.data_fattura,
  C.nome AS nome_cliente,
  C.cognome AS cognome_cliente
FROM fatture AS F
JOIN clienti AS C
  ON F.id_cliente = C.numero_cliente
WHERE
  F.importo < 1000;


SELECT
  F.numero_fattura,
  F.importo,
  F.iva,
  F.data_fattura,
  FO.denominazione AS nome_fornitore
FROM fatture AS F
JOIN fornitori AS FO
  ON F.numero_fornitore = FO.numero_fornitore;

SELECT
  EXTRACT(YEAR FROM data_fattura) AS anno,
  COUNT(numero_fattura) AS numero_di_fatture
FROM fatture
WHERE
  iva = 20.00
GROUP BY
  anno
ORDER BY
  anno;


SELECT
  EXTRACT(YEAR FROM data_fattura) AS anno,
  COUNT(numero_fattura) AS numero_di_fatture,
  SUM(importo) AS somma_importi
FROM fatture
GROUP BY
  anno
ORDER BY
  anno;

SELECT EXTRACT(YEAR FROM data_fattura) AS anno
FROM fatture
WHERE tipologia = 'A'
GROUP BY anno
HAVING COUNT(*) > 2;