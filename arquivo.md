sudo bash -lc '
echo "== DIRS ==" &&
ls -ld /opt/SoftwareExpress/sitef \
       /opt/SoftwareExpress/sitef/package/linux \
       /opt/SoftwareExpress/sitef/scripts &&

echo "== RPM ==" &&
ls -l /opt/SoftwareExpress/sitef/package/linux &&

echo "== SCRIPTS ==" &&
ls -la /opt/SoftwareExpress/sitef/scripts &&

echo "== INIT FILES ==" &&
test -f /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/init_parallel.sh && echo "OK init_parallel.sh" &&
test -f /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/init.sh && echo "OK init.sh" &&

echo "== PROBE ==" &&
ls -la /opt/SoftwareExpress/sitef/.predeploy_probe_* 2>/dev/null || echo "Probe nao encontrado" &&

echo "== PARALLEL.TXT ==" &&
cat /opt/SoftwareExpress/sitef/scripts/deploy-sitef-0.0.1/parallel.txt 2>/dev/null || echo "parallel.txt nao existe" &&

echo "== PKGS INSTALADOS (se aplicavel) ==" &&
rpm -qa | grep -i sitef || true
'
