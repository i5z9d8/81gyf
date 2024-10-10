import os
import subprocess
import sys
import urllib.request
import tarfile

def run_command(command, check=True):
    """
    Exécute une commande shell.
    """
    print(f"Exécution de la commande: {' '.join(command)}")
    result = subprocess.run(command, stdout=subprocess.PIPE, stderr=subprocess.PIPE, text=True)
    if check and result.returncode != 0:
        print(f"Erreur lors de l'exécution de la commande: {' '.join(command)}")
        print(f"STDOUT: {result.stdout}")
        print(f"STDERR: {result.stderr}")
        sys.exit(result.returncode)
    return result

def check_root():
    """
    Vérifie si le script est exécuté en tant que root.
    """
    if os.geteuid() != 0:
        print("Ce script doit être exécuté en tant que root. Utilisez sudo.")
        sys.exit(1)

def check_dpkg():
    """
    Vérifie si dpkg est déjà installé.
    """
    result = subprocess.run(['which', 'dpkg'], stdout=subprocess.PIPE, stderr=subprocess.PIPE)
    if result.returncode == 0:
        print("dpkg est déjà installé.")
        sys.exit(0)

def install_build_dependencies():
    """
    Installe les dépendances nécessaires pour compiler dpkg.
    """
    print("Installation des dépendances de compilation...")
    # Ceci suppose que vous avez au moins un gestionnaire de paquets fonctionnel
    # Par exemple, utiliser apt si disponible
    if shutil.which('apt-get'):
        run_command(['apt-get', 'update'])
        run_command(['apt-get', 'install', '-y', 'build-essential', 'libc6-dev', 'libselinux1-dev', 'liblzma-dev', 'libbz2-dev', 'libgmp-dev', 'gettext'])
    elif shutil.which('yum'):
        run_command(['yum', 'groupinstall', '-y', 'Development Tools'])
        run_command(['yum', 'install', '-y', 'glibc-devel', 'libselinux-devel', 'xz-devel', 'bzip2-devel', 'gmp-devel', 'gettext'])
    else:
        print("Gestionnaire de paquets non pris en charge. Veuillez installer manuellement les dépendances de compilation.")
        sys.exit(1)

def download_dpkg(version='1.21.20160110'):
    """
    Télécharge le paquet source de dpkg.
    """
    url = f"https://salsa.debian.org/dpkg-team/dpkg/-/archive/{version}/dpkg-{version}.tar.gz"
    tarball = f"dpkg-{version}.tar.gz"
    print(f"Téléchargement de dpkg depuis {url}...")
    try:
        urllib.request.urlretrieve(url, tarball)
    except Exception as e:
        print(f"Erreur lors du téléchargement de dpkg: {e}")
        sys.exit(1)
    return tarball

def extract_tarball(tarball):
    """
    Extrait le tarball téléchargé.
    """
    print(f"Extraction de {tarball}...")
    try:
        with tarfile.open(tarball, "r:gz") as tar:
            tar.extractall()
    except Exception as e:
        print(f"Erreur lors de l'extraction de {tarball}: {e}")
        sys.exit(1)
    extracted_dir = tarball.replace('.tar.gz', '')
    return extracted_dir

def compile_and_install(source_dir):
    """
    Compile et installe dpkg à partir du répertoire source.
    """
    os.chdir(source_dir)
    print("Configuration de la compilation...")
    run_command(['./configure'])
    print("Compilation de dpkg...")
    run_command(['make'])
    print("Installation de dpkg...")
    run_command(['make', 'install'])
    print("dpkg a été installé avec succès.")

def main():
    check_root()
    check_dpkg()
    install_build_dependencies()
    tarball = download_dpkg()
    source_dir = extract_tarball(tarball)
    compile_and_install(source_dir)
    print("Nettoyage des fichiers temporaires...")
    os.remove(tarball)
    # Optionnel : supprimer le répertoire source
    # shutil.rmtree(source_dir)
    print("Installation de dpkg terminée.")

if __name__ == "__main__":
    import shutil
    main()
