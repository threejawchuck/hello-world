import os
import sys
import tempfile

class Tee(object):
    def __init__(self, fd1, fd2):
        self.fd1 = fd1
        self.fd2 = fd2
    def write(self, data):
        self.fd1.write(data)
        self.fd2.write(data)
    def flush(self):
        self.fd1.flush()
        self.fd2.flush()
    def fileno(self):
        self.fd2.fileno()

class idBuffering:
    def __init__(self, spawn):
        self.spawn = spawn
    def buffered_spawn(self, sh, escape, cmd, args, env):
        temp_file = tempfile.TemporaryFile()
        retval = self.spawn(sh, escape, cmd, args, env, temp_file, temp_file)
        temp_file.seek(0)
        sys.stdout.write(temp_file.read())
        temp_file.close()
        return retval

# Create Build Environment
env = Environment()

# Insert logging hook into the build environment
env['SPAWN'] = idBuffering(env['PSPAWN']).buffered_spawn
env['LOG'] = os.path.abspath(os.path.join('log', 'Build.log'))

# Insert man in the middle on stderr/stdout to capture and log
if not os.path.isdir(os.path.dirname(env['LOG'])):
    os.makedirs(os.path.dirname(env['LOG']))
log_file_object = open(env['LOG'], 'w')
stderr = sys.stderr
stdout = sys.stdout
sys.stderr = Tee(stderr, log_file_object)
sys.stdout = Tee(stdout, log_file_object)
env['STDERR'] = sys.stderr
env['STDOUT'] = sys.stdout

# Export the Environment
Export('env')

# Call SConscript
SConscript('SConscript')
