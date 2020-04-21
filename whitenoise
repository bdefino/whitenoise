#!/usr/bin/env python3
import aifc
import getopt
import os
import sys

__doc__ = """white noise file generator
Usage: %s [OPTIONS] PATH
OPTIONS
	-c INT
		the total frame count
		(defaults to -1)
	-h
		print this text and exit
	-l INT
		frame length
		(in bytes; defaults to 4)
	-r INT
		frame rate
		(per second; defaults to 1024)
PATH
	output path
	(`-` for STDOUT)"""

def help(name):
	print(__doc__ % name, file = sys.stderr)

def main(argv):
	count = -1
	fps = 1024
	framelen = 4
	opts, args = getopt.getopt(argv[1:], "c:hl:r:+")
	path = None

	if not len(args) == 1:
		help(argv[0])
		return 1
	path = args[0]

	for k, v in opts:
		if k == "-c":
			try:
				count = int(v)
			except ValueError:
				print("Frame count must be an integer.",
					file = sys.stderr)
				return 1
		elif k == "-h":
			help(argv[0])
			return 0
		elif k == "-l":
			try:
				framelen = int(v)

				if framelen <= 0:
					raise ValueError()
			except ValueError:
				print("Frame length must be a natural number.",
					file = sys.stderr)
				return 1
		elif k == "-r":
			try:
				fps = int(v)

				if fps <= 0:
					raise ValueError()
			except ValueError:
				print("FPS must be a natural number.",
					file = sys.stderr)
				return 1
		else:
			print("Unrecognized option.", file = sys.stderr)
			help(argv[0])
			return 1

	try:
		if path == '-':
			whitenoise(os.fdopen(sys.stdout.fileno(), "wb"), count,
				fps, framelen,)
		else:
			with open(path, "wb") as fp:
				whitenoise(fp, count, fps, framelen)
	except BrokenPipeError:
		pass
	except KeyboardInterrupt:
		raise KeyboardInterrupt()
	except Exception as e:
		print(e, file = sys.stderr)
		return 1
	return 0

def whitenoise(fp, count = -1, fps = 1024, framelen = 4):
	"""populate a file with white noise"""
	with aifc.open(fp, 'w') as afp:
		# prep

		afp.setsampwidth(framelen)
		afp.setframerate(fps)
		afp.setnchannels(2)

		# write

		buflen = framelen * fps
		remaining = count * framelen

		while remaining:
			n = buflen if remaining < 0 \
				or buflen < remaining else remaining
			afp.writeframes(os.urandom(n))
			fp.flush()
			os.fdatasync(fp.fileno())
			remaining -= n

if __name__ == "__main__":
	sys.exit(main(sys.argv))

