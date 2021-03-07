# mysql_sys.x-ps_digest_95th_percentile_by_avg_us-

SELECT 
    `sys`.`s2`.`avg_us` AS `avg_us`,
    IFNULL((SUM(`sys`.`s1`.`cnt`) / NULLIF((SELECT 
                            COUNT(0)
                        FROM
                            `performance_schema`.`events_statements_summary_by_digest`),
                    0)),
            0) AS `percentile`
FROM
    (`sys`.`x$ps_digest_avg_latency_distribution` `s1`
    JOIN `sys`.`x$ps_digest_avg_latency_distribution` `s2` ON ((`sys`.`s1`.`avg_us` <= `sys`.`s2`.`avg_us`)))
GROUP BY `sys`.`s2`.`avg_us`
HAVING (IFNULL((SUM(`sys`.`s1`.`cnt`) / NULLIF((SELECT 
                        COUNT(0)
                    FROM
                        `performance_schema`.`events_statements_summary_by_digest`),
                0)),
        0) > 0.95)
ORDER BY `percentile`
LIMIT 1
