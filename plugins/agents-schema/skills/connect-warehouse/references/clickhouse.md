# ClickHouse

Use the clickhouse-connect driver over the HTTP interface.

1. Read `host`, `port`, `user`, `password`, and `secure` from `agents.yml`. Ask the user to add
   any missing value without pasting secrets into chat. `port` defaults to 8443 when `secure`
   is true and 8123 otherwise; `user` defaults to `default`.
2. Install `clickhouse-connect` when `clickhouse_connect` is unavailable.
3. Verify the connection by replacing `<SQL>` with `SELECT 1`:

   ```bash
   python3 - <<'PYEOF'
   import json
   import clickhouse_connect
   import yaml

   cfg = yaml.safe_load(open("agents.yml"))
   client = clickhouse_connect.get_client(
       host=cfg["host"],
       port=cfg.get("port"),
       username=cfg.get("user", "default"),
       password=cfg["password"],
       secure=cfg.get("secure", True),
   )
   result = client.query("""
   <SQL>
   """)
   rows = [dict(zip(result.column_names, row)) for row in result.result_rows]
   print(json.dumps(rows, indent=2, default=str))
   PYEOF
   ```

Note: ClickHouse identifiers are case-sensitive and the metadata lives in the
lowercase `agents` database (`agents.root`, not `AGENTS.ROOT`).
