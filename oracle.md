# Oracle

## Query Cheat Sheet

### - 테이블 명세서 추출 스크립트 ++
- Java Model Text
- mybatis Coloum Text
- AX Grid Coloum Text
- Html Form Input Text
- Table Description
``` sql
SELECT TABLE_NAME,
       IDX,
       COLUMN_NAME,
       COLUMN_COMMENT,
       COLUMN_NAME_PASCAL,
       CONCAT(
               '/** ', COLUMN_COMMENT, ' */', char(10), 'private ',
               CASE
                   WHEN (DATA_TYPE = 'varchar' OR DATA_TYPE = 'char' OR DATA_TYPE = 'date' OR DATA_TYPE = 'datetime' OR
                         DATA_TYPE = 'time' OR DATA_TYPE = 'text' OR DATA_TYPE = 'tinytext' OR
                         DATA_TYPE = 'mediumtext' OR DATA_TYPE = 'longtext') THEN 'String '
                   WHEN (DATA_TYPE = 'bit' OR DATA_TYPE = 'tinyint' OR DATA_TYPE = 'smallint' OR
                         DATA_TYPE = 'mediumint' OR DATA_TYPE = 'bigint' OR DATA_TYPE = 'int' OR
                         DATA_TYPE = 'integer' OR DATA_TYPE = 'timestamp') THEN 'int '
                   WHEN (DATA_TYPE = 'real') THEN 'long '
                   WHEN (DATA_TYPE = 'double' OR DATA_TYPE = 'numeric' OR DATA_TYPE = 'decimal') THEN 'double '
                   WHEN (DATA_TYPE = 'float') THEN 'float '
                   WHEN (DATA_TYPE = 'tinyblob' OR DATA_TYPE = 'mediumblob' OR DATA_TYPE = 'bigblob' OR
                         DATA_TYPE = 'blob') THEN 'byte[] '
                   ELSE 'String '
                   END,
               COLUMN_NAME_PASCAL, ';'
           )                                                   AS COLUMN_CODE,
       CONCAT(COLUMN_NAME, ' AS ', COLUMN_NAME_PASCAL)         AS COLUMN_ALIAS,
       CONCAT(COLUMN_NAME, ',')                                AS COLUMN_INSERT,
       CONCAT('#{', COLUMN_NAME_PASCAL, '}, ')                 AS COLUMN_INSERT_VALUE,
       CONCAT(COLUMN_NAME, ' = #{', COLUMN_NAME_PASCAL, '}, ') AS COLUMN_UPDATE,
       CONCAT_WS('',
                 '{key:""', COLUMN_NAME_PASCAL, '"" label:""', COLUMN_COMMENT, '"", width:',
                 CASE
                     WHEN (DATA_TYPE = 'varchar' OR DATA_TYPE = 'char') THEN CAST(DATA_LENGTH / 2 AS signed integer)
                     WHEN (DATA_TYPE = 'bit' OR DATA_TYPE = 'tinyint' OR DATA_TYPE = 'smallint' OR
                           DATA_TYPE = 'mediumint' OR DATA_TYPE = 'bigint' OR DATA_TYPE = 'int' OR
                           DATA_TYPE = 'integer' OR DATA_TYPE = 'timestamp' OR DATA_TYPE = 'double' OR
                           DATA_TYPE = 'numeric' OR DATA_TYPE = 'decimal') THEN 60
                     WHEN (DATA_TYPE = 'text' OR DATA_TYPE = 'tinytext' OR DATA_TYPE = 'mediumtext' OR
                           DATA_TYPE = 'longtext' OR DATA_TYPE = 'tinyblob' OR DATA_TYPE = 'mediumblob' OR
                           DATA_TYPE = 'bigblob' OR DATA_TYPE = 'blob') THEN 240
                     WHEN (DATA_TYPE = 'date' OR DATA_TYPE = 'datetime' OR DATA_TYPE = 'time' OR DATA_TYPE = 'bit' OR
                           DATA_TYPE = 'tinyint' OR DATA_TYPE = 'smallint' OR DATA_TYPE = 'mediumint' OR
                           DATA_TYPE = 'bigint' OR DATA_TYPE = 'int' OR DATA_TYPE = 'integer' OR
                           DATA_TYPE = 'timestamp' OR DATA_TYPE = 'real' OR DATA_TYPE = 'double' OR 'numeric' OR
                           DATA_TYPE = 'decimal' OR DATA_TYPE = 'float') THEN 70
                     ELSE 150
                     END
           ,
                 '}, '
           )                                                   AS AX_GRID_COLUMN,
       CASE
           WHEN (DATA_TYPE = 'text' OR DATA_TYPE = 'tinytext' OR DATA_TYPE = 'mediumtext' OR DATA_TYPE = 'longtext')
               THEN
               CONCAT_WS('', '<tr>', char(10), char(9), '<th>', COLUMN_COMMENT, '</th>', char(10), char(9), char(9),
                         '<textarea name=""', COLUMN_NAME_PASCAL, '"" id=""', COLUMN_NAME_PASCAL,
                         '"" class=""full"" row=""5"" maxlength=""', CAST(DATA_LENGTH / 2 AS signed integer),
                         '"" itemname=""', COLUMN_COMMENT, '"" placeholder=""', COLUMN_COMMENT, '을(를) 입력해주세요.""',
                         '>${info.', COLUMN_NAME_PASCAL, '}</textarea>')
           WHEN (DATA_TYPE = 'char') THEN
               CONCAT_WS('', '<tr>', char(10), char(9), '<th>', COLUMN_COMMENT, '</th>', char(10), char(9), char(9),
                         '<td>', char(10), char(9), char(9), '<select name=""', COLUMN_NAME_PASCAL, '"" id=""',
                         COLUMN_NAME_PASCAL, '"" class=""full"" required itemname=""', COLUMN_COMMENT, '"">', char(10),
                         char(9), char(9), '<option value="""">선택하세요</option>', char(10), char(9), char(9),
                         '<option value=""Y"" <c:if test=""${info.', COLUMN_NAME_PASCAL,
                         '}==\""Y\"""">selected=""selected""</c:if>사용</option>', char(10), char(9), char(9),
                         '<option value=""N"" <c:if test=""${info.', COLUMN_NAME_PASCAL,
                         '}!=\""Y\"""">selected=""selected""</c:if>미사용</option>', char(10), char(9), char(9),
                         '</select>', '</td>', char(10), char(9), '</tr>')
           WHEN (DATA_TYPE = 'tinyblob' OR DATA_TYPE = 'mediumblob' OR DATA_TYPE = 'bigblob' OR DATA_TYPE = 'blob') THEN
               CONCAT_WS('', '<tr>', char(10), char(9), '<th>', COLUMN_COMMENT, '</th>', char(10), '<td>', char(10),
                         '<input type=""file"" name=""', COLUMN_NAME_PASCAL, '"" id=""', COLUMN_NAME_PASCAL,
                         '"" class=""full"" value=""${info.', COLUMN_NAME_PASCAL, '}"" maxlength=""',
                         CAST(DATA_LENGTH / 2 AS signed integer), '"" required itemname=""', COLUMN_COMMENT,
                         '"" placeholder=""', COLUMN_COMMENT, '을(를) 선택해주세요.""', '/>', '</td>', char(10), char(9),
                         char(9), '</tr>')
           WHEN (DATA_TYPE = 'bit' OR DATA_TYPE = 'tinyint' OR DATA_TYPE = 'smallint' OR DATA_TYPE = 'mediumint' OR
                 DATA_TYPE = 'bigint' OR DATA_TYPE = 'int' OR DATA_TYPE = 'integer' OR DATA_TYPE = 'timestamp' OR
                 DATA_TYPE = 'double' OR DATA_TYPE = 'numeric' OR DATA_TYPE = 'decimal') THEN
               CONCAT_WS('', '<tr>', char(10), char(9), '<th>', COLUMN_COMMENT, '</th>', char(10), char(9), char(9),
                         '<td>', char(10), char(9), char(9), '<input type=""text"" name=""', COLUMN_NAME_PASCAL,
                         '"" id=""', COLUMN_NAME_PASCAL, '"" class=""full"" value=""${info.', COLUMN_NAME_PASCAL,
                         '}"" maxlength=""', 11, '"" required itemname=""', COLUMN_COMMENT, '"" placeholder=""',
                         COLUMN_COMMENT, '을(를) 입력해주세요.""', '/>', '</td>', char(10), char(9), char(9), '</tr>')
           ELSE
               CONCAT_WS('', '<tr>', char(10), char(9), '<th>', COLUMN_COMMENT, '</th>', char(10), char(9), char(9),
                         '<td>', char(10), char(9), char(9), '<input type=""text"" name=""', COLUMN_NAME_PASCAL,
                         '"" id=""', COLUMN_NAME_PASCAL, '"" class=""full"" value=""${info.', COLUMN_NAME_PASCAL,
                         '}"" maxlength=""', CAST(DATA_LENGTH / 2 AS signed integer), '"" required itemname=""',
                         COLUMN_COMMENT, '"" placeholder=""', COLUMN_COMMENT, '을(를) 입력해주세요.""', '/>', '</td>', char(10),
                         char(9), char(9), '</tr>')
           END                                                 AS FORM_INPUT_STRING,
       DATA_TYPE,
       DATA_LENGTH,
       FULL_DATA_TYPE,
       IS_PRIMARY_KEY,
       IS_NULL,
       IS_AUTO_INCREMENT,
       DEFAULT_VALUE
FROM (
         SELECT TABLE_NAME,
                IDX,
                COLUMN_NAME,
                COLUMN_COMMENT,
                CONCAT(
                        CONCAT(LOWER(SUBSTRING(SPRIT_COLUMN1, 1, 1)), LOWER(SUBSTRING(SPRIT_COLUMN1, 2))),
                        CONCAT(UPPER(SUBSTRING(SPRIT_COLUMN2, 1, 1)), LOWER(SUBSTRING(SPRIT_COLUMN2, 2))),
                        CONCAT(UPPER(SUBSTRING(SPRIT_COLUMN3, 1, 1)), LOWER(SUBSTRING(SPRIT_COLUMN3, 2)))
                    ) AS COLUMN_NAME_PASCAL,
                DATA_TYPE,
                DATA_LENGTH,
                FULL_DATA_TYPE,
                IS_PRIMARY_KEY,
                IS_NULL,
                IS_AUTO_INCREMENT,
                DEFAULT_VALUE
         FROM (
                  SELECT TABLE_NAME,
                         ORDINAL_POSITION                     AS IDX,
                         COLUMN_NAME                          AS COLUMN_NAME,
                         SUBSTRING_INDEX(COLUMN_NAME, '_', 1) AS SPRIT_COLUMN1,
                         REPLACE(REPLACE(SUBSTRING_INDEX(COLUMN_NAME, '_', 2), SUBSTRING_INDEX(COLUMN_NAME, '_', 1),
                                         ''), '_', '')        AS SPRIT_COLUMN2,
                         REPLACE(REPLACE(SUBSTRING_INDEX(COLUMN_NAME, '_', 3), SUBSTRING_INDEX(COLUMN_NAME, '_', 2),
                                         ''), '_', '')        AS SPRIT_COLUMN3,
                         DATA_TYPE,
                         -- CHARACTER_MAXIMUM_LENGTH AS DATA_LENGTH,
                         CASE
                             WHEN (DATA_TYPE = 'varchar' OR DATA_TYPE = 'char') THEN CHARACTER_MAXIMUM_LENGTH
                             WHEN (DATA_TYPE = 'bit' OR DATA_TYPE = 'tinyint' OR DATA_TYPE = 'smallint' OR
                                   DATA_TYPE = 'mediumint' OR DATA_TYPE = 'bigint' OR DATA_TYPE = 'int' OR
                                   DATA_TYPE = 'integer') THEN NUMERIC_PRECISION + 1
                             WHEN (DATA_TYPE = 'real' OR DATA_TYPE = 'double' OR DATA_TYPE = 'numeric' OR
                                   DATA_TYPE = 'decimal') THEN CONCAT(NUMERIC_PRECISION, '.', NUMERIC_SCALE)
                             WHEN (DATA_TYPE = 'text' OR DATA_TYPE = 'tinytext' OR DATA_TYPE = 'mediumtext' OR
                                   DATA_TYPE = 'longtext' OR DATA_TYPE = 'tinyblob' OR DATA_TYPE = 'mediumblob' OR
                                   DATA_TYPE = 'bigblob' OR DATA_TYPE = 'blob') THEN 240
                             ELSE CHARACTER_MAXIMUM_LENGTH
                             END                              AS DATA_LENGTH,
                         COLUMN_TYPE                          AS FULL_DATA_TYPE,
                         (
                             CASE COLUMN_KEY WHEN 'PRI' THEN 'YES' ELSE 'NO' END
                             )                                AS IS_PRIMARY_KEY,
                         IS_NULLABLE                          AS IS_NULL,
                         (
                             CASE EXTRA WHEN 'auto_increment' THEN 'YES' ELSE 'NO' END
                             )                                AS IS_AUTO_INCREMENT,
                         COLUMN_DEFAULT                       AS DEFAULT_VALUE,
                         COLUMN_COMMENT
                  FROM INFORMATION_SCHEMA.COLUMNS
                  WHERE TABLE_SCHEMA = 'MOONSONG_CMS_NEW'
                        --  AND TABLE_NAME = 'ACCOUNT_INFO'
                  ORDER BY TABLE_NAME, ORDINAL_POSITION
              ) AS TMP
     ) AS TMP_TABLE
     ```