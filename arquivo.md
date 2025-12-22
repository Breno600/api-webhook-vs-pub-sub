# Export do output variable (compatível com diferentes executores)
if [ -n "${HARNESS_OUTPUT_VARIABLES:-}" ]; then
  echo "SECRET_JSON=$SECRET_JSON" >> "$HARNESS_OUTPUT_VARIABLES"
elif [ -n "${HARNESS_ENV_EXPORT:-}" ]; then
  echo "SECRET_JSON=$SECRET_JSON" >> "$HARNESS_ENV_EXPORT"
else
  echo "ERRO: Harness não forneceu arquivo de output (HARNESS_OUTPUT_VARIABLES/HARNESS_ENV_EXPORT)."
  exit 1
fi