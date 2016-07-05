---
layout: post
title: tomcat启动带有netty的项目后无法关闭的解决方案
category: 技术
keywords: server
---

修改io.netty.util.concurrent.DefaultThreadFactory这个类。

原代码：

```java
/*
 * Copyright 2013 The Netty Project
 *
 * The Netty Project licenses this file to you under the Apache License，
 * version 2.0 (the "License"); you may not use this file except in compliance
 * with the License. You may obtain a copy of the License at:
 *
 *   http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing， software
 * distributed under the License is distributed on an "AS IS" BASIS， WITHOUT
 * WARRANTIES OR CONDITIONS OF ANY KIND， either express or implied. See the
 * License for the specific language governing permissions and limitations
 * under the License.
 */

package io.netty.util.concurrent;

import io.netty.util.internal.StringUtil;

import java.util.Locale;
import java.util.concurrent.ThreadFactory;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * A {@link ThreadFactory} implementation with a simple naming rule.
 */
public class DefaultThreadFactory implements ThreadFactory {

    private static final AtomicInteger poolId = new AtomicInteger();

    private final AtomicInteger nextId = new AtomicInteger();
    private final String prefix;
    private final boolean daemon;
    private final int priority;

    public DefaultThreadFactory(Class<?> poolType) {
        this(poolType， false， Thread.NORM_PRIORITY);
    }

    public DefaultThreadFactory(String poolName) {
        this(poolName， false， Thread.NORM_PRIORITY);
    }

    public DefaultThreadFactory(Class<?> poolType， boolean daemon) {
        this(poolType， daemon， Thread.NORM_PRIORITY);
    }

    public DefaultThreadFactory(String poolName， boolean daemon) {
        this(poolName， daemon， Thread.NORM_PRIORITY);
    }

    public DefaultThreadFactory(Class<?> poolType， int priority) {
        this(poolType， false， priority);
    }

    public DefaultThreadFactory(String poolName， int priority) {
        this(poolName， false， priority);
    }

    public DefaultThreadFactory(Class<?> poolType， boolean daemon， int priority) {
        this(toPoolName(poolType)， daemon， priority);
    }

    private static String toPoolName(Class<?> poolType) {
        if (poolType == null) {
            throw new NullPointerException("poolType");
        }

        String poolName = StringUtil.simpleClassName(poolType);
        switch (poolName.length()) {
            case 0:
                return "unknown";
            case 1:
                return poolName.toLowerCase(Locale.US);
            default:
                if (Character.isUpperCase(poolName.charAt(0)) && Character.isLowerCase(poolName.charAt(1))) {
                    return Character.toLowerCase(poolName.charAt(0)) + poolName.substring(1);
                } else {
                    return poolName;
                }
        }
    }

    public DefaultThreadFactory(String poolName， boolean daemon， int priority) {
        if (poolName == null) {
            throw new NullPointerException("poolName");
        }
        if (priority < Thread.MIN_PRIORITY || priority > Thread.MAX_PRIORITY) {
            throw new IllegalArgumentException(
                    "priority: " + priority + " (expected: Thread.MIN_PRIORITY <= priority <= Thread.MAX_PRIORITY)");
        }

        prefix = poolName + '-' + poolId.incrementAndGet() + '-';
        this.daemon = daemon;
        this.priority = priority;
    }

    @Override
    public Thread newThread(Runnable r) {
        Thread t = newThread(new DefaultRunnableDecorator(r)， prefix + nextId.incrementAndGet());
        try {
            if (t.isDaemon()) {
                if (!daemon) {
                    t.setDaemon(false);
                }
            } else {
                if (daemon) {
                    t.setDaemon(true);
                }
            }

            if (t.getPriority() != priority) {
                t.setPriority(priority);
            }
        } catch (Exception ignored) {
            // Doesn't matter even if failed to set.
        }
        return t;
    }

    protected Thread newThread(Runnable r， String name) {
        return new FastThreadLocalThread(r， name);
    }

    private static final class DefaultRunnableDecorator implements Runnable {

        private final Runnable r;

        DefaultRunnableDecorator(Runnable r) {
            this.r = r;
        }

        @Override
        public void run() {
            try {
                r.run();
            } finally {
                FastThreadLocal.removeAll();
            }
        }
    }
}

```

修改后代码：

```java
/*
 * Copyright 2013 The Netty Project
 *
 * The Netty Project licenses this file to you under the Apache License，
 * version 2.0 (the "License"); you may not use this file except in compliance
 * with the License. You may obtain a copy of the License at:
 *
 *   http://www.apache.org/licenses/LICENSE-2.0
 *
 * Unless required by applicable law or agreed to in writing， software
 * distributed under the License is distributed on an "AS IS" BASIS， WITHOUT
 * WARRANTIES OR CONDITIONS OF ANY KIND， either express or implied. See the
 * License for the specific language governing permissions and limitations
 * under the License.
 */

package io.netty.util.concurrent;

import io.netty.util.internal.StringUtil;

import java.util.Locale;
import java.util.concurrent.ThreadFactory;
import java.util.concurrent.atomic.AtomicInteger;

/**
 * A {@link ThreadFactory} implementation with a simple naming rule.
 */
public class DefaultThreadFactory implements ThreadFactory {

	private static final AtomicInteger poolId = new AtomicInteger();

	private final AtomicInteger nextId = new AtomicInteger();
	private final String prefix;
	private final int priority;

	public DefaultThreadFactory(Class<?> poolType) {
		this(poolType， false， Thread.NORM_PRIORITY);
	}

	public DefaultThreadFactory(String poolName) {
		this(poolName， false， Thread.NORM_PRIORITY);
	}

	public DefaultThreadFactory(Class<?> poolType， boolean daemon) {
		this(poolType， daemon， Thread.NORM_PRIORITY);
	}

	public DefaultThreadFactory(String poolName， boolean daemon) {
		this(poolName， daemon， Thread.NORM_PRIORITY);
	}

	public DefaultThreadFactory(Class<?> poolType， int priority) {
		this(poolType， false， priority);
	}

	public DefaultThreadFactory(String poolName， int priority) {
		this(poolName， false， priority);
	}

	public DefaultThreadFactory(Class<?> poolType， boolean daemon， int priority) {
		this(toPoolName(poolType)， daemon， priority);
	}

	private static String toPoolName(Class<?> poolType) {
		if (poolType == null) {
			throw new NullPointerException("poolType");
		}

		String poolName = StringUtil.simpleClassName(poolType);
		switch (poolName.length()) {
		case 0:
			return "unknown";
		case 1:
			return poolName.toLowerCase(Locale.US);
		default:
			if (Character.isUpperCase(poolName.charAt(0))
					&& Character.isLowerCase(poolName.charAt(1))) {
				return Character.toLowerCase(poolName.charAt(0))
						+ poolName.substring(1);
			} else {
				return poolName;
			}
		}
	}

	public DefaultThreadFactory(String poolName， boolean daemon， int priority) {
		if (poolName == null) {
			throw new NullPointerException("poolName");
		}
		if (priority < Thread.MIN_PRIORITY || priority > Thread.MAX_PRIORITY) {
			throw new IllegalArgumentException(
					"priority: "
							+ priority
							+ " (expected: Thread.MIN_PRIORITY <= priority <= Thread.MAX_PRIORITY)");
		}

		prefix = poolName + '-' + poolId.incrementAndGet() + '-';
		this.priority = priority;
	}

	@Override
	public Thread newThread(Runnable r) {
		Thread t = newThread(new DefaultRunnableDecorator(r)，
				prefix + nextId.incrementAndGet());
		try {
			t.setDaemon(true);

			if (t.getPriority() != priority) {
				t.setPriority(priority);
			}
		} catch (Exception ignored) {
			// Doesn't matter even if failed to set.
		}
		return t;
	}

	protected Thread newThread(Runnable r， String name) {
		return new FastThreadLocalThread(r， name);
	}

	private static final class DefaultRunnableDecorator implements Runnable {

		private final Runnable r;

		DefaultRunnableDecorator(Runnable r) {
			this.r = r;
		}

		@Override
		public void run() {
			try {
				r.run();
			} finally {
				FastThreadLocal.removeAll();
			}
		}
	}
}

```

主要是这句代码：t.setDaemon(true);<br>
就是将当前进程变成后台进程。<br>
如果对某个线程对象在启动(调用start方法)之前调用了setDaemon(true)方法，这个线程就变成了后台线程。对java程序来说，只要还有一个前台线程在运行，这个进程就不会结束，如果一个进程中只有后台线程运行，这个进程会结束。