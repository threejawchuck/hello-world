# file: SConstruct
import os

def variantglob(env, pattern, ondisk=True, source=True, strings=False,
                recursive=False):
    matches = []
    if isinstance(pattern, basestring):
        patterns = [pattern]
    if isinstance(pattern, (list, tuple, set)):
        patterns = pattern
    for root, dirs, filenames in os.walk(env['SOURCE_DIR']):
        cwd = Dir(os.path.join(env['VARIANT_DIR'],
                               os.path.relpath(root, env['SOURCE_DIR'])))
        matches.extend([src for lst in [cwd.glob(p, ondisk, source, strings)
                                        for p in patterns] for src in lst])
    return matches

# Create Build Environment
Import('env')

# Customize Environment
exe_env = env.Clone()
exe_env.Replace(VARIANT_DIR='build',
                SOURCE_DIR='src')
exe_env.Append(CPPPATH=['include'],
               CCFLAGS=['-std=c++11'])

# Setup Variant Directory
VariantDir(variant_dir=exe_env['VARIANT_DIR'],
           src_dir=exe_env['SOURCE_DIR'], duplicate=0)

# Build the executable
exe = exe_env.Program(os.path.join(exe_env['VARIANT_DIR'], 'example'),
                      variantglob(exe_env, '*.cpp', recursive=True))

# Install the executable
Install('bin', exe)
