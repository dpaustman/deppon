wget http://archive.download.redhat.com/pub/redhat/linux/7.0/en/os/i386/RedHat/RPMS/rpm-build-4.0-4.i386.rpm
https://www.cnblogs.com/wongbingming/p/11086773.html
#-*- coding: utf-8 -*-
import time
from kazoo.client import KazooClient
from kazoo.recipe.watchers import ChildrenWatch

class ValidatorDetector:

    def __init__(self):
        self.zk = KazooClient(hosts='192.168.18.99:2181,192.168.18.99:2182,192.168.18.99:2183')
        self.validator_children_watcher = ChildrenWatch(client=self.zk,path='/mproxy/validators',func=self.validator_watcher_fun)
        self.zk.start()

    def validator_watcher_fun(self,children):
        print("The children now are: %s" % children)

    # def create_node(self):
    #     self.zk.create('/mproxy/validators/validator',b'validator_huabei_1',ephemeral=True,sequence=True,makepath=True)

    def __del__(self):
        self.zk.close()

    def create_node(self, path, value=b'', acl=None, ephemeral=False, sequence=False,makepath=False, include_data=False):
        self.create(path, value, ephemeral=True, sequence=True, makepath=True)

    def query(self, path, watch=None):
        node = self.get_children('%s'.format(path))
        print(node)

    def update(self, path, value, version=-1):
        self.set('%s', b'%s') % (path, value)

    def delete_node(self, path, version=-1, recursive=False):
        self.delete('path', recursive=True)



if __name__ == '__main__':
    detector = ValidatorDetector()
    detector.create_node('/data', 'nihao')
    detector.query()
    time.sleep(10)
https://blog.csdn.net/u012965373/article/details/77896512
https://blog.csdn.net/book_mmicky/article/details/39956809
https://blog.csdn.net/qq_45834006/article/details/105785139
https://blog.csdn.net/lzm1340458776/article/details/43227951 pingjun
https://blog.csdn.net/qq_41458071/article/details/105777874?
https://blog.csdn.net/hpu11/article/details/53326069

https://blog.csdn.net/u012373815/article/details/73257754 rpm打包


