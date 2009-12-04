# vim: ft=python expandtab

import re
import os
from site_init import GBuilder, Initialize

opts = Variables()
opts.Add(PathVariable('PREFIX', 'Installation prefix', os.path.expanduser('~/FOSS'), PathVariable.PathIsDirCreate))
opts.Add(BoolVariable('DEBUG', 'Build with Debugging information', 0))
opts.Add(PathVariable('PERL', 'Path to the executable perl', r'C:\Perl\bin\perl.exe', PathVariable.PathIsFile))

env = Environment(variables = opts,
                  ENV=os.environ, tools = ['default', GBuilder])

Initialize(env)

def modify_makefile(target, source, env):
    o = open(str(target[0]), 'w')
    i = open(str(source[0]), 'r')
    lib = re.compile('lib /out:')
    for line in i:
        if not lib.search(line):
            o.write(line)
        else:
            o.write('\tlink /DLL /out:libbz2%s.dll /DEF:libbz2-nodll.def /IMPLIB:libbz2.lib $(OBJS)\n' % env['LIB_SUFFIX'])
    i.close()
    o.close()
env.Command('makefile.scons', 'makefile.msc', modify_makefile)
env.Command('libbz2-nodll.def', 'libbz2.def', "sed -e '/LIBRARY /d' -e '/DESCRIPTION/d' < $SOURCE > $TARGET") 
dll = env.Command(['libbz2' + env['LIB_SUFFIX'] + '.dll', 'libbz2.lib'], ['makefile.scons', 'libbz2-nodll.def'], 'nmake -f $SOURCE lib')

env.AddPostAction(dll, 'mt.exe -nologo -manifest ${TARGET}.manifest -outputresource:$TARGET;2')

env.Alias('install', env.Install('$PREFIX/include/', 'bzlib.h'))
env.Alias('install', env.Install('$PREFIX/bin', 'libbz2' + env['LIB_SUFFIX'] + '.dll'))
env.Alias('install', env.Install('$PREFIX/lib',  'libbz2.lib'))
env.Alias('install', env.InstallAs('$PREFIX/lib/bz2.lib',  'libbz2.lib'))
