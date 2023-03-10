
<strong>Explicação das colunas</strong>
<br>
* <strong> Passengerird:</strong> Identificador único para cada passageiro;
* <strong> Survived:</strong> Indica se o passageiro sobreviveu, onde (0-Não; 1-Sim)
* <strong> Pclass:</strong> Classe do passageiro (1 - Primeira Classe; 2- Segunda Classe; 3- Terceira Classe;
* <strong> Name:</strong> Nome do passageiro;
* <strong> Sex:</strong> Sexo do passageiro;
* <strong> Age:</strong> Idade do passageiro;
* <strong> SibSp:</strong> Número de irmãos - esposas a bordo;
* <strong> Parch:</strong> Numero de pais-filhos a bordo;
* <strong> Ticket:</strong> Número da passagem;
* <strong> Fare:</strong> Preço da passagem;
* <strong> Cabin:</strong> Cabine;
* <strong> Embarked:</strong> Local onde o passageiro embarcou (C=Cherbourg, Q= Queenstown, S= Southampton)

## Visualização da base de dados
``` Sql
-- Visualização da base
select
  *
from
  titanic
limit(5);
```
![1certo](https://user-images.githubusercontent.com/124627259/224395474-a4b689a4-a55e-4097-8f47-588b11a5693e.PNG)



## Panorama geral dos dados
``` Sql
select * from titanic
```
![2](https://user-images.githubusercontent.com/124627259/224395563-a27f5d5a-5194-45b6-9447-8fc9569564ba.PNG)
![3](https://user-images.githubusercontent.com/124627259/224395590-c6cbae47-30d8-4c83-b16a-2de290805702.PNG)

```sql
-- Qunatidade de Passageiros por gênero
SELECT
  Sex,
  count(*) as Contagem_sex
FROM
  titanic
group by
  Sex
```
![4](https://user-images.githubusercontent.com/124627259/224395671-a1714009-2fe8-4970-b671-45cc38d87d38.PNG)

```sql
-- Percentual de Sobreviventes
SELECT
  COUNT(*) AS contagem,
  case
    when survived = 1 then 'Sobreviventes'
    when survived = 0 then 'Não sobreviventes'
  end as Sobreviventes
FROM
  titanic
GROUP BY
  survived;
```
![5](https://user-images.githubusercontent.com/124627259/224395817-6d211c88-1663-4b29-bbe0-1caaf709d0ed.PNG)

```sql
-- Quantidade de Passageiros por idade
select
  Age,
  count(*) as contagem_Passageiro
from
  titanic
group by
  Age
order by
  Age
```
![6](https://user-images.githubusercontent.com/124627259/224395885-66d45f66-f524-446a-9e1b-99a21c1ce8e3.PNG)

```sql
- Fazendo a trataiva das idades que contém valores nulos
-- Primeiro iremos verificar a Idade média filtrada por classe e genero
select
  Pclass,
  Sex,
  avg(Age)
from
  titanic
group by
  Pclass,
  Sex
order by
  Pclass
```
![7](https://user-images.githubusercontent.com/124627259/224396639-c1b19893-9eb8-4755-98f4-3f9009f9d5ed.PNG)

```sql
-- Comando para fazer alteração no databricks
CREATE TABLE titanic_delt USING DELTA AS
SELECT
  *
FROM
  titanic;
```
```sql
-- Adicionando a média de idade aos passegeiros aos dados nulos, filtrados por classe e gênero
UPDATE
  titanic_delt
SET
  Age = CASE
    WHEN Age IS NULL THEN (
      SELECT
        AVG(Age)
      FROM
        titanic_delt AS t2
      WHERE
        t2.Sex = titanic_delt.Sex
        AND t2.Pclass = titanic_delt.Pclass
        AND t2.Age IS NOT NULL
    )
    ELSE Age
  END;
```

```sql
-- Analisando novamente a Quantidade de Passageiros por idade com os dados nulos corrigidos
select
  Age,
  count(*) as contagem_Passageiro
from
  titanic_delt
group by
  Age
order by
  Age
```
![8](https://user-images.githubusercontent.com/124627259/224396298-f55856dc-ac8e-45cb-9afc-2260df799866.PNG)

```sql
-- Idade média dos Passageiros
select
  Sex,
  avg(Age) as idade_media
from
  titanic_delt
group by
  Sex
```
![9](https://user-images.githubusercontent.com/124627259/224396727-91f646db-8acb-432a-9e00-858f841082c1.PNG)
```sql
-- Total de sobreviventes e não sobreviventes por idade média
SELECT
  Sex,
  case
    when Survived = 0 then 'Não_sobrevivente'
    when Survived = 1 then 'Sobrevivente '
  end as Sobreviventes,
  Avg(Age) as idade_media
from
  titanic_delt
group by
  Sex,
  Survived
```
![10](https://user-images.githubusercontent.com/124627259/224396838-c5ae5125-429b-4d00-9178-62b0feff9282.PNG)


```sql
-- Quantidade de sobreviventes por faixa etária
SELECT
  SUM(
    CASE
      WHEN Age < 12 THEN 1
      ELSE 0
    END
  ) AS Criancas_ate_12,
  SUM(
    CASE
      WHEN Age BETWEEN 12 AND 17 THEN 1
      ELSE 0
    END
  ) AS Jovem_12_aos_17,
  SUM(
    CASE
      WHEN Age BETWEEN 18 AND 65 THEN 1
      ELSE 0
    END
  ) AS Adulto_18_aos_65
FROM
  titanic_delt;
```
![11](https://user-images.githubusercontent.com/124627259/224396872-5db4855f-1090-4330-96fe-0dff9f9f1089.PNG)

```sql
-- Média de sobreviventes por Idade
SELECT
  CASE
    WHEN Age <= 12 THEN 'Crianças'
    WHEN Age > 12
    AND Age <= 65 THEN 'Adultos'
    ELSE 'Senhores'
  END AS Faixa_Etaria,
  AVG(Survived) AS Media_Sobrevivencia
FROM
  titanic_delt
GROUP BY
  CASE
    WHEN Age <= 12 THEN 'Crianças'
    WHEN Age > 12
    AND Age <= 65 THEN 'Adultos'
    ELSE 'Senhores'
  END
ORDER BY
  Media_Sobrevivencia desc;
```
![12](https://user-images.githubusercontent.com/124627259/224396959-b4b247f5-b44b-4381-b535-697fbccae102.PNG)


```sql
-- Total de passageiros por classe
select
  Pclass,
  count(*) as total_por_classe
from
  titanic_delt
group by
  Pclass
```
![13](https://user-images.githubusercontent.com/124627259/224396997-ad42fffb-b0fd-4122-b56d-6d216d81448d.PNG)

```sql
-- Percentual de sobreviventes por classe
select
  Pclass,
  count(*) as sobreviventes
from
  titanic_delt
where
  Survived = 1
group by
  Pclass
order by
  Pclass
```
![14](https://user-images.githubusercontent.com/124627259/224397059-5109351a-567c-41ac-af62-c03a9f14d146.PNG)

```sql
-- Média de sobreviventes por classe
SELECT
  Pclass,
  AVG(Survived) AS avg_survived
FROM
  titanic_delt
GROUP BY
  Pclass
```
![15](https://user-images.githubusercontent.com/124627259/224397117-7e69a6bd-912e-44eb-9197-96d03d22125e.PNG)

```sql
-- Não sobreviventes por classe
select
  Pclass,
  count(*)
from
  titanic_delt
where
  survived = 0
group by
  Pclass
```
![16](https://user-images.githubusercontent.com/124627259/224397145-1aac55e6-6009-4e8e-af9f-6c01a0dd6229.PNG)

```sql
-- Total de Passageiros por local de embarque
select
  Embarked,
  count(*) as total_embarque
from
  titanic_delt
where
  Embarked is not null
group by
  Embarked
```
![17](https://user-images.githubusercontent.com/124627259/224397171-d6a95689-0f6f-41d3-866b-e870f3c7cfbe.PNG)

```sql
-- Total de sobreviventes por local de embarque
SELECT
  Embarked,
  count(Survived) AS total_sobreviventes
FROM
  titanic
WHERE
  Embarked IS NOT NULL
  AND Survived = 1
GROUP BY
  Embarked;
```
![18](https://user-images.githubusercontent.com/124627259/224397197-06de891d-2c8d-4b2c-b2cf-445c173569cd.PNG)

```sql
--Sobreviventes por preço de ticket
SELECT
  Pclass,
  COUNT(*) AS total,
  SUM(
    CASE
      WHEN Survived = 1 THEN 1
      ELSE 0
    END
  ) AS Sobrevivente,
  SUM(
    CASE
      WHEN Survived = 0 THEN 1
      ELSE 0
    END
  ) AS N ã o_sobrevivente,
  (
    SUM(
      CASE
        WHEN Survived = 1 THEN 1
        ELSE 0
      END
    ) * 1.0 / COUNT(*)
  ) AS survival_rate
FROM
  titanic_delt
GROUP BY
  Pclass;
```
![19](https://user-images.githubusercontent.com/124627259/224397224-86674bd2-3459-4ec3-b806-2f4f18752560.PNG)

```sql
-- Quantidade de Passageiros com irmãos a bordo por classe, mostrando a idade média e numero de sobreviventes
SELECT
  Pclass,
  COUNT(*) AS Num_Passageiros,
  AVG(Age) AS Idade_media,
  SUM(Survived) AS Num_Sobreviventes
FROM
  titanic_delt
WHERE
  SibSp > 0
GROUP BY
  Pclass;
 ```
 ![20](https://user-images.githubusercontent.com/124627259/224397266-40de2409-7727-4343-85d7-b59c4b41d00b.PNG)

 ```sql
 --Quantidadede Passageiros com filhos a bordo por classe
Select
  Pclass,
  Count(*) as Num_Passageiros,
  avg(Age) as Idade_media,
  sum(Survived) as Num_sobreviventes
from
  titanic_delt
where
  Parch > 0
group by
  Pclass
 ```
 ![22certo](https://user-images.githubusercontent.com/124627259/224397285-17e01115-ae65-4178-84ad-d0f0a56f20d6.PNG)




























