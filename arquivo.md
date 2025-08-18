stages: [detect]

detect:changes:
  stage: detect
  image: alpine:3.20
  before_script:
    - apk add --no-cache git jq
    # garante histórico completo (runner pode vir com shallow clone)
    - |
      if [ -f .git/shallow ]; then
        git fetch --unshallow --tags
      else
        git fetch --all --tags
      fi
  script:
    - echo "Pipeline source: $CI_PIPELINE_SOURCE"
    - echo "Current SHA:     $CI_COMMIT_SHA"
    - echo "Before SHA:      $CI_COMMIT_BEFORE_SHA"
    - echo "Target branch:   ${CI_MERGE_REQUEST_TARGET_BRANCH_NAME:-N/A}"
    - echo "Source branch:   ${CI_MERGE_REQUEST_SOURCE_BRANCH_NAME:-$CI_COMMIT_REF_NAME}"

    # calcula o intervalo correto dependendo do tipo da pipeline
    - |
      if [ -n "$CI_MERGE_REQUEST_TARGET_BRANCH_SHA" ]; then
        echo "[MR] Detectando mudanças entre origem e destino da MR…"
        BASE="$CI_MERGE_REQUEST_TARGET_BRANCH_SHA"
        HEAD="$CI_MERGE_REQUEST_SOURCE_BRANCH_SHA"
        # se SHA da origem não vier, use HEAD atual
        HEAD="${HEAD:-$CI_COMMIT_SHA}"
        git fetch origin "$CI_MERGE_REQUEST_TARGET_BRANCH_NAME"
      else
        echo "[Push] Detectando mudanças do push…"
        BASE="$CI_COMMIT_BEFORE_SHA"
        HEAD="$CI_COMMIT_SHA"
        # primeiro commit no branch vem com 000000..., usa merge-base com main como fallback
        if [ "$BASE" = "0000000000000000000000000000000000000000" ] || [ -z "$BASE" ]; then
          # ajuste "main" → seu branch padrão se for "master" etc.
          git fetch origin main
          BASE=$(git merge-base "origin/main" "$HEAD")
          echo "BASE não informado; usando merge-base com origin/main: $BASE"
        fi
      fi

    # lista os arquivos alterados (adicionados, modificados, renomeados e deletados)
    - |
      echo "Arquivos alterados:"
      git diff --name-status "$BASE" "$HEAD"

      echo "Somente nomes:"
      CHANGED_FILES=$(git diff --name-only --diff-filter=ACMR "$BASE" "$HEAD" | sort -u)
      printf '%s\n' "$CHANGED_FILES"

    # (opcional) exporta em JSON para consumir em outros jobs
    - |
      printf '%s\n' "$CHANGED_FILES" | jq -R . | jq -s . > changed-files.json
      echo "JSON salvo em changed-files.json:"
      cat changed-files.json

  artifacts:
    when: always
    paths:
      - changed-files.json
    expire_in: 1 day

  rules:
    - if: $CI_PIPELINE_SOURCE == "merge_request_event"
    - if: $CI_PIPELINE_SOURCE == "push"
