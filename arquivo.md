#!/usr/bin/env bash
set -euo pipefail

# ----------------------------
# Helpers
# ----------------------------
log() { echo "[$(date '+%Y-%m-%d %H:%M:%S')] $*"; }
die() { echo "ERROR: $*" >&2; exit 1; }

# ----------------------------
# Inputs (passed by Ansible)
# Example: --machine sitef-01 --env dev
# ----------------------------
MACHINE=""
ENVIRONMENT=""

while [[ $# -gt 0 ]]; do
  case "$1" in
    --machine) MACHINE="${2:-}"; shift 2 ;;
    --env) ENVIRONMENT="${2:-}"; shift 2 ;;
    *) log "Unknown arg: $1"; shift ;;
  esac
done

# ----------------------------
# Paths (must match Ansible)
# ----------------------------
SCRIPTS_DIR="$(cd "$(dirname "${BASH_SOURCE[0]}")" && pwd)"
BASE_DIR="$(cd "${SCRIPTS_DIR}/.." && pwd)"
DOWNLOAD_DIR="${BASE_DIR}/downloads"
CURRENT_DIR="${BASE_DIR}/current"
RELEASES_DIR="${BASE_DIR}/releases"
LOG_DIR="${BASE_DIR}/logs"

log "Starting init.sh"
log "Machine=${MACHINE:-<not_set>} Env=${ENVIRONMENT:-<not_set>}"
log "Base=${BASE_DIR}"
log "Download=${DOWNLOAD_DIR}"

# ----------------------------
# Prechecks
# ----------------------------
[[ -d "${DOWNLOAD_DIR}" ]] || die "download dir not found: ${DOWNLOAD_DIR}"

mkdir -p "${CURRENT_DIR}" "${RELEASES_DIR}" "${LOG_DIR}"

# ----------------------------
# Decide what you downloaded
# Example: you download app.tar.gz from S3
# ----------------------------
ARTIFACT_TGZ="${DOWNLOAD_DIR}/app.tar.gz"
SERVICE_NAME="sitef-app"
RELEASE_ID="$(date '+%Y%m%d%H%M%S')"
RELEASE_PATH="${RELEASES_DIR}/${RELEASE_ID}"

if [[ ! -f "${ARTIFACT_TGZ}" ]]; then
  log "Files found in download dir:"
  ls -lah "${DOWNLOAD_DIR}" || true
  die "Expected artifact not found: ${ARTIFACT_TGZ}"
fi

# ----------------------------
# Install / Deploy
# ----------------------------
log "Creating release path: ${RELEASE_PATH}"
mkdir -p "${RELEASE_PATH}"

log "Extracting artifact: ${ARTIFACT_TGZ}"
tar -xzf "${ARTIFACT_TGZ}" -C "${RELEASE_PATH}"

# If your tar already has a folder, you can adjust:
# tar -xzf "${ARTIFACT_TGZ}" -C "${RELEASE_PATH}" --strip-components=1

log "Updating current symlink"
ln -sfn "${RELEASE_PATH}" "${CURRENT_DIR}"

# Example: permissions
chmod -R 0755 "${CURRENT_DIR}"

# ----------------------------
# Service handling (systemd example)
# ----------------------------
if command -v systemctl >/dev/null 2>&1; then
  log "Reloading systemd"
  systemctl daemon-reload || true

  log "Restarting service: ${SERVICE_NAME}"
  systemctl restart "${SERVICE_NAME}" || die "Failed to restart ${SERVICE_NAME}"

  log "Checking status: ${SERVICE_NAME}"
  systemctl --no-pager status "${SERVICE_NAME}" || true
else
  log "systemctl not found. Skipping service restart."
fi

log "Deployment finished successfully."
