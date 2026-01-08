#!/usr/bin/env bash
set -euo pipefail

log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*"; }
die() { echo "ERROR: $*" >&2; exit 1; }

# -------- Inputs (optional) --------
BASE_DIR="${BASE_DIR:-/opt/SoftwareExpress/sitef}"
CFG_TGZ="${CFG_TGZ:-${BASE_DIR}/config-migrado.tgz}"
CFG_DIR="${CFG_DIR:-${BASE_DIR}/config}"
OWNER_USER="${OWNER_USER:-sitefd}"
OWNER_GROUP="${OWNER_GROUP:-sitef}"

# If you want the backup folder name fixed by date:
TODAY="$(date '+%Y-%m-%d')"
CFG_BACKUP="${CFG_BACKUP:-${BASE_DIR}/config-${TODAY}}"

# Cleanup controls
CLEANUP_TGZ="${CLEANUP_TGZ:-true}"          # true/false
CLEANUP_SCRIPT="${CLEANUP_SCRIPT:-true}"    # true/false

log "Running init.sh"
log "BASE_DIR=${BASE_DIR}"
log "CFG_TGZ=${CFG_TGZ}"
log "CFG_DIR=${CFG_DIR}"
log "CFG_BACKUP=${CFG_BACKUP}"
log "OWNER=${OWNER_USER}:${OWNER_GROUP}"

cd "${BASE_DIR}" || die "Base dir not found: ${BASE_DIR}"

# 1) Backup existing config (if present)
if [[ -d "${CFG_DIR}" ]]; then
  log "Backing up existing config -> ${CFG_BACKUP}"
  # if backup already exists, add timestamp to avoid overwrite
  if [[ -e "${CFG_BACKUP}" ]]; then
    TS="$(date '+%H%M%S')"
    CFG_BACKUP="${CFG_BACKUP}-${TS}"
    log "Backup folder already exists, using: ${CFG_BACKUP}"
  fi
  mv "${CFG_DIR}" "${CFG_BACKUP}"
else
  log "No existing config folder to backup."
fi

# 2) Extract new config
[[ -f "${CFG_TGZ}" ]] || die "Config tgz not found: ${CFG_TGZ}"

log "Extracting ${CFG_TGZ} ..."
tar -xf "${CFG_TGZ}"

# 3) Ensure config exists after extraction
[[ -d "${CFG_DIR}" ]] || die "Config folder not found after extract: ${CFG_DIR}"

# 4) Set correct ownership
log "Setting ownership: ${OWNER_USER}:${OWNER_GROUP} on ${CFG_DIR}"
chown -hR "${OWNER_USER}:${OWNER_GROUP}" "${CFG_DIR}"

# 5) Cleanup the tgz downloaded from S3
if [[ "${CLEANUP_TGZ}" == "true" ]]; then
  log "Removing artifact: ${CFG_TGZ}"
  rm -f "${CFG_TGZ}"
fi

# 6) Cleanup this script (downloaded from repo to the server)
if [[ "${CLEANUP_SCRIPT}" == "true" ]]; then
  SELF_PATH="$(readlink -f "$0" || echo "$0")"
  log "Removing script: ${SELF_PATH}"
  rm -f "${SELF_PATH}" || true
fi

log "Done."
